---
title: Migrace virtuálních počítačů do Azure Premium Storage | Microsoft Docs
description: Migrujte existující virtuální počítače na Azure Premium Storage. Premium Storage nabízí podporu vysoce výkonné, nízkou latencí disku pro I náročnými úlohy běžící na virtuálních počítačích Azure.
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: 36ff73d36c752fb342dcfff2360b4f6f7013740e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "27993910"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrace na Azure Premium Storage (nespravované disků)

> [!NOTE]
> Tento článek popisuje, jak migrovat virtuální počítač, který používá nespravované standardní disky pro virtuální počítač, který používá nespravované prémiové disky. Doporučujeme použít Azure spravované disky pro nové virtuální počítače, a převést vaše předchozí disky nespravované na spravované disky. Spravované disky popisovač základní účty úložiště, nemusíte. Další informace najdete v tématu naše [přehled disky spravované](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage nabízí podporu vysoce výkonné, nízkou latencí disku pro virtuální počítače spuštěné I náročnými úlohy. Pomocí migrace vaší aplikace disky virtuálních počítačů do Azure Premium Storage můžete využít výhod rychlosti a výkonu z těchto disků.

Účel tohoto průvodce je pomoct noví uživatelé Azure Premium Storage lepší Příprava na s hladkým přechodem z jejich aktuálního systému do úložiště úrovně Premium. Průvodce řeší tři klíčové komponenty v tomto procesu:

* [Plánování migrace na Storage úrovně Premium](#plan-the-migration-to-premium-storage)
* [Příprava a zkopírovat virtuální pevné disky (VHD) do úložiště úrovně Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Vytvoření virtuálního počítače Azure pomocí služby Storage úrovně Premium](#create-azure-virtual-machine-using-premium-storage)

Můžete migrovat virtuální počítače z jiné platformy Azure Premium Storage nebo migrovat existující virtuální počítače Azure ze standardního úložiště do úložiště úrovně Premium. Tento průvodce popisuje kroky pro oba dva scénáře. Postupujte podle kroků v příslušné části v závislosti na vašem scénáři zadaný.

> [!NOTE]
> Najdete přehled funkcí a cen. úložiště Premium Storage v úložiště Premium: [vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../virtual-machines/windows/premium-storage.md). Doporučujeme migraci všech disku virtuálního počítače vyžadující vysokou IOPS na Azure Premium Storage k dosažení nejlepšího výkonu pro vaši aplikaci. Pokud disk nevyžadují vysokou IOPS, můžete omezit náklady na údržbu ve standardní úložiště, která ukládá data disku virtuálního počítače na jednotkách pevných disků (HDD) namísto SSD.
>

Dokončení procesu migrace v celé jeho šíři může vyžadovat další akce před i po podle kroků uvedených v této příručce. Mezi příklady patří konfiguraci virtuální sítě nebo koncových bodů nebo provádění změn kódu v aplikaci sám sebe, což může vyžadovat výpadky v aplikaci. Tyto akce jsou jedinečné pro každou aplikaci a měli byste pokračovat společně s podle kroků uvedených v této příručce na přechod úplné do úložiště úrovně Premium jako bezproblémové nejblíže.

## <a name="plan-the-migration-to-premium-storage"></a>Plánování migrace na Storage úrovně Premium
Tato část zajistí připravení postupujte podle kroků migrace v tomto článku a může být nejlepší rozhodnutí o typech virtuálního počítače a disku.

### <a name="prerequisites"></a>Požadavky
* Budete potřebovat předplatné Azure. Pokud nemáte, můžete vytvořit jeden měsíc [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) předplatné nebo navštivte [Azure – ceny](https://azure.microsoft.com/pricing/) další možnosti.
* Spuštění rutiny prostředí PowerShell, je nutné modul Microsoft Azure PowerShell. Bod instalace a pokyny pro instalaci jsou popsané v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
* Když budete chtít použít Azure virtuálních počítačů spuštěných na Storage úrovně Premium, budete muset použít podporující virtuálních počítačů služby Storage úrovně Premium. Standard a Premium Storage disků můžete použít s podporou virtuálních počítačů služby Storage úrovně Premium. V budoucnu bude k dispozici s více typy virtuálních počítačů disky úložiště Premium. Další informace o všech dostupných typů disku virtuálního počítače Azure a velikosti najdete v tématu [velikosti virtuálních počítačů](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [velikosti pro cloudové služby](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Požadavky
Virtuální počítač Azure podporuje připojení několik disků úložiště Premium tak, aby vaše aplikace může mít až 256 TB úložiště na virtuální počítač. Storage úrovně Premium můžete aplikace dosáhnout 80 000 IOPS (vstupně výstupních operací za sekundu) na virtuální počítač a 2000 MB za druhé propustnost disku na virtuální počítač s velmi nízkou latenci pro operace čtení. Máte možností v různých virtuálních počítačů a disků. Tato část se můžete k vyhledání možnost, která nejlépe vyhovuje vaší zatížení.

#### <a name="vm-sizes"></a>Velikost virtuálních počítačů
Specifikace velikosti virtuálního počítače Azure, jsou uvedeny v [velikosti virtuálních počítačů](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Zkontrolujte vlastnosti výkonu virtuálních počítačů, které pracovat Storage úrovně Premium a vyberete nejvhodnější velikost virtuálního počítače, který nejlépe vyhovuje vaší zatížení. Ujistěte se, že je dostatečnou šířku pásma dostupné na vašem virtuálním počítači k řízení provozu disku.

#### <a name="disk-sizes"></a>Velikost disků
Existuje pět typů disků, které lze použít s vaší virtuálních počítačů a každá z nich má konkrétní IOPs a propustnost omezení. Vzít v úvahu tyto limity, když vyberete typ disku pro virtuální počítač podle potřeb vaší aplikace z hlediska kapacity, výkon, škálovatelnost a načte ve špičce.

| Disky typu Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Velikost disku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Vstupně-výstupní operace za sekundu / disk       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Propustnost / disk | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

V závislosti na velikosti pracovní zátěže určí, jestli dalších datových disků jsou nezbytné pro virtuální počítač. Několik trvalých datových disků můžete připojit k virtuálnímu počítači. V případě potřeby můžete rozkládají mezi disky tím zvýšit kapacitu a výkon svazku. (Zjistíte novinky prokládání disků [zde](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Pokud rozkládají Storage úrovně Premium datových disků pomocí [prostory úložiště][4], byste měli nakonfigurovat s jedním sloupcem pro každý disk, který se používá. Celkový výkon prokládané svazku, jinak hodnota může být nižší než očekávaný kvůli nevyrovnaná distribuce přenosů mezi disky. Pro virtuální počítače s Linuxem můžete použít *mdadm* nástroj k dosažení stejné. Najdete v článku [konfigurace RAID softwaru v systému Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobnosti.

#### <a name="storage-account-scalability-targets"></a>Cíle škálovatelnosti účtu Storage
Prémiové účty úložiště mají následující cíle škálovatelnosti kromě [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md). Pokud vaše požadavky aplikací přesahují cíle škálovatelnosti účtu jedno úložiště, sestavení aplikace pomocí více účtů úložiště a oddílu data mezi různými tyto účty úložiště.

| Celkový počet účet kapacity | Celková šířka pásma pro účet místně redundantní úložiště |
|:--- |:--- |
| Kapacita disku: 35TB<br />Pořízení snímku kapacity: 10 TB |Až 50 gigabity za sekundu pro příchozí a odchozí |

Další informace o specifikacích Premium Storage, podívejte se na [škálovatelnost a cíle výkonnosti při použití služby Premium Storage](../../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku
Ve výchozím nastavení, je disk ukládání do mezipaměti zásad *jen pro čtení* pro všechny Premium datových disků, a *pro čtení a zápis* pro disk operačního systému Premium připojen k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro dosažení optimálního výkonu pro vaše aplikace IOs. Těžký zápisu nebo pouze pro zápis datové disky (například soubory protokolu serveru SQL Server) zakažte ukládání do mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace. Nastavení mezipaměti pro existující datových disků můžete aktualizovat pomocí [portálu Azure](https://portal.azure.com) nebo *- HostCaching* parametr *Set-AzureDataDisk* rutiny.

#### <a name="location"></a>Umístění
Vyberte umístění, kde je k dispozici Azure Premium Storage. V tématu [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění. Virtuální počítače umístěné ve stejné oblasti jako účet úložiště, že disky pro virtuální počítač bude Story mnohem lepší výkon než pokud jsou v oblastech.

#### <a name="other-azure-vm-configuration-settings"></a>Další nastavení konfigurace virtuálního počítače Azure
Při vytváření virtuálního počítače Azure, zobrazí se výzva k nakonfigurovat některá nastavení virtuálního počítače. Pamatujte si, že se několik nastavení opravě po dobu jeho existence virtuálního počítače, zatímco můžete upravit nebo jiné přidat později. Zkontrolujte nastavení konfigurace virtuálního počítače Azure a ujistěte se, zda tyto jsou správně nakonfigurována tak, aby odpovídaly vašim požadavkům zatížení.

### <a name="optimization"></a>Optimalizace
[Azure Premium Storage: Návrh vysoce výkonné](../../virtual-machines/windows/premium-storage-performance.md) poskytuje pokyny pro vytváření vysoce výkonné aplikace pomocí Azure Premium Storage. Můžete postupujte podle pokynů v kombinaci s osvědčené postupy z hlediska výkonu pro technologie, které používá vaše aplikace.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Příprava a zkopírovat virtuální pevné disky (VHD) do úložiště úrovně Premium
Následující část obsahuje pokyny pro přípravu virtuální pevné disky z virtuálního počítače a kopírování virtuálních pevných disků do úložiště Azure.

* [Scénář 1: "I se migruje existující virtuální počítače Azure do Azure Storage úrovně Premium."](#scenario1)
* [Scénář 2: "I mě migrace virtuálních počítačů z jiných platformách, na Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Požadavky
Příprava na migraci virtuální pevné disky, budete potřebovat:

* Předplatné Azure, účet úložiště a kontejneru v daném účtu úložiště, do které můžete zkopírovat svůj disk VHD. Všimněte si, že cílový účet úložiště může být účet Standard nebo Premium Storage podle potřeby.
* Nástroj pro zobecnění virtuální pevný disk, pokud budete chtít vytvořit více instancí virtuálního počítače z něj. Například nástroj sysprep pro systém Windows nebo virt.krychle sysprep pro Ubuntu.
* Nástroj pro nahrání souboru virtuálního pevného disku do účtu úložiště. V tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md) nebo použijte [Azure storage Exploreru](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Tato příručka popisuje kopírování svůj disk VHD pomocí nástroje AzCopy.

> [!NOTE]
> Pokud zvolíte možnost synchronní kopie s AzCopy, optimální výkon, zkopírujte svůj disk VHD spuštěním jednoho z těchto nástrojů z virtuálního počítače Azure, který je ve stejné oblasti jako cílový účet úložiště. Pokud kopírujete virtuální pevný disk z virtuálního počítače Azure v jiné oblasti, může být pomalejší výkon.
>
> Kopírování velkého množství dat přes omezenou šířkou pásma, zvažte [k přenosu dat do Blob Storage pomocí služby Azure Import/Export](../storage-import-export-service.md); to umožňuje přenos dat pomocí přesouvání pevných disků o datovém centru Azure. Služba Azure Import/Export můžete použít ke zkopírování dat na účet úložiště standard storage. Jakmile jsou data ve vašem účtu standardní úložiště, můžete použít buď [kopírovat objekt Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) nebo AzCopy k přenosu dat do účtu úložiště premium.
>
> Všimněte si, že Microsoft Azure podporuje pouze soubory virtuálního pevného disku pevné velikosti. Soubory VHDX nebo dynamické virtuální pevné disky nejsou podporovány. Pokud máte dynamický virtuální pevný disk, můžete ji převést na pevné velikosti pomocí [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) rutiny.
>
>

### <a name="scenario1"></a>Scénář 1: "I se migruje existující virtuální počítače Azure do Azure Storage úrovně Premium."
Pokud migrujete existující virtuální počítače Azure, zastavte virtuální počítač, virtuální pevné disky podle typu virtuálního pevného disku, které chcete připravit a poté zkopírujte virtuální pevný disk s AzCopy nebo prostředí PowerShell.

Virtuální počítač musí být úplně dolů k migraci do čistého stavu. Nedojde výpadku až po dokončení migrace.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Příprava na migraci virtuálních pevných disků
Pokud migrujete existující virtuální počítače Azure do úložiště úrovně Premium, může být svůj disk VHD:

* Bitovou kopii operačního systému
* Disk jedinečné operačního systému
* Datový disk

Níže budeme provede tyto 3 scénáře pro přípravu svůj disk VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Pomocí zobecněný virtuální pevný disk operačního systému můžete vytvořit více instancí virtuálního počítače
Pokud nahráváte virtuálního pevného disku, který se použije k vytvoření více obecné instancí virtuálního počítače Azure, musíte nejprve generalize virtuální pevný disk pomocí nástroje sysprep. To se vztahuje na virtuální pevný disk, který je místně nebo v cloudu. Nástroj Sysprep odebere všechny informace specifické pro počítač z virtuálního pevného disku.

> [!IMPORTANT]
> Pořízení snímku nebo zálohování virtuálního počítače před jeho generalizací. Spuštěný nástroj sysprep zastaví a navrátit instance virtuálního počítače. Postupujte podle kroků pro nástroj sysprep virtuálního pevného disku operačního systému Windows. Všimněte si, že spustíte příkaz Sysprep bude vyžadovat, abyste vypnout virtuální počítač. Další informace o nástroji Sysprep najdete v tématu [přehled nástroje Sysprep](http://technet.microsoft.com/library/hh825209.aspx) nebo [technické informace o nástroji Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otevřete okno příkazového řádku jako správce.
2. Zadejte následující příkaz, chcete-li spustit nástroj Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. V nástroji pro přípravu systému vyberte prostředí systému zadejte Out-of-Box (při prvním zapnutí), zaškrtněte políčko generalizace, vyberte **vypnutí**a potom klikněte na **OK**, jak je znázorněno na obrázku níže. Nástroj Sysprep odstraní zobecní operační systém a vypnutí systému.

    ![][1]

U virtuálního počítače s Ubuntu použijte virt.krychle sysprep k dosažení stejné. V tématu [virt.krychle sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) další podrobnosti. Viz také některé open source [Linux serveru zřizování softwaru](http://www.cyberciti.biz/tips/server-provisioning-software.html) pro jiné operační systémy Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>K vytvoření jedné instance virtuálního počítače použijte jedinečný virtuální pevný disk operačního systému
Pokud máte aplikace běžící na virtuálním počítači, který vyžaduje konkrétní data počítače, není generalize virtuální pevný disk. Zobecněný virtuální pevný disk lze použít k vytvoření jedinečné instance virtuálního počítače Azure. Například pokud máte řadiče domény na svůj disk VHD, provádění sysprep znamená, že neúčinná jako řadič domény. Zkontrolujte aplikací běžících na virtuální počítač a dopad spuštění nástroje sysprep v jejich před generalizací virtuální pevný disk.

##### <a name="register-data-disk-vhd"></a>Registrovat datový disk VHD
Pokud máte v Azure a migrovat datových disků, musí se zkontrolujte, zda jsou virtuální počítače, které používají tyto datové disky vypnout.

Postupujte podle kroků popsaných níže zkopírujte virtuální pevný disk do Azure Premium Storage a zaregistrovat ho jako zřízené datový disk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvořit cíl pro svůj disk VHD
Vytvořte účet úložiště pro údržbu virtuální pevné disky. Při plánování kam se mají ukládat virtuální pevné disky, zvažte následující body:

* Cílový účet úložiště Premium.
* Umístění účtu úložiště musí být stejná jako úložiště Premium podporuje virtuální počítače Azure se vytvoří v konečné fázi. Může zkopírovat nového účtu úložiště, nebo se chystáte použít stejný účet úložiště na základě potřeb.
* Zkopírujte a uložte klíč účtu úložiště cílový účet úložiště pro další fáze.

Pro datové disky můžete si nechat některé datové disky v standardní účet úložiště (například disky, které obsahují chladič úložiště), ale důrazně doporučujeme přesouvání všech dat pro produkční pracovního vytížení k používání služby storage úrovně premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3. Zkopírujte virtuální pevný disk s AzCopy nebo prostředí PowerShell
Musíte se najít klíč kontejneru úložiště a cesta účtu ke zpracování některá z těchto dvou možností. Klíč účtu úložiště a cesta kontejneru lze nalézt v **portálu Azure** > **úložiště**. Kontejner, bude mít adresu URL jako "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Možnost 1: Zkopírujte virtuální pevný disk s AzCopy (asynchronní kopie)
Pomocí AzCopy, můžete snadno nahrávat VHD přes Internet. V závislosti na velikosti virtuálních pevných disků může to trvat čas. Mějte na paměti, zkontrolujte vstupní/výstupní limity účtu úložiště při použití této možnosti. V tématu [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md) podrobnosti.

1. Stáhněte a nainstalujte AzCopy odsud: [nejnovější verzi AzCopy](http://aka.ms/downloadazcopy)
2. Otevřete prostředí Azure PowerShell a přejděte do složky, kde je nainstalován nástroj AzCopy.
3. Pomocí následujícího příkazu zkopírujte soubor virtuálního pevného disku z "Zdroje" do "Cíle".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Příklad:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Zde je uveden popis použité v příkazu AzCopy parametry:

   * **/ Zdroj:  *&lt;zdroj&gt;:***  umístění složky nebo URL adresa kontejneru úložiště, který obsahuje virtuální pevný disk.
   * **/ SourceKey:  *&lt;klíč účtu zdroj&gt;:***  klíč účtu úložiště zdrojového účtu úložiště.
   * **/ Cíle:  *&lt;cílové&gt;:***  úložiště URL adresa kontejneru VHD, který chcete zkopírovat.
   * **/ DestKey:  *&lt;klíč účtu cíle&gt;:***  klíč účtu úložiště cílový účet úložiště.
   * **/ Vzor:  *&lt;název souboru&gt;:***  zadejte název souboru virtuálního pevného disku ke kopírování.

Podrobnosti o použití nástroje AzCopy nástroje najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Možnost 2: Zkopírujte virtuální pevný disk pomocí prostředí PowerShell (Synchronized kopie)
Můžete také zkopírovat soubor virtuálního pevného disku pomocí rutiny prostředí PowerShell Start AzureStorageBlobCopy. Pomocí následujícího příkazu v prostředí Azure PowerShell zkopírujte virtuální pevný disk. Nahraďte hodnoty v <> s odpovídajícími hodnotami z vašeho zdrojového a cílového účtu úložiště. Chcete-li použít tento příkaz, musíte mít volat virtuální pevné disky ve vašem účtu úložiště cílový kontejner. Pokud kontejner neexistuje, vytvořte ji před spuštěním příkazu.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Příklad:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scénář 2: "I mě migrace virtuálních počítačů z jiných platformách, na Azure Premium Storage."
Pokud migrujete virtuální pevný disk z jiných - cloudové úložiště Azure do Azure, musíte napřed exportovat virtuální pevný disk do místního adresáře. Úplný zdrojový cesty místnímu adresáři, kde je uložený užitečný virtuálního pevného disku a potom pomocí AzCopy nahrajte ho do úložiště Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Export virtuálního pevného disku do místního adresáře
##### <a name="copy-a-vhd-from-aws"></a>Zkopírujte virtuální pevný disk z AWS
1. Pokud používáte AWS, vyexportovat do disku VHD v sady Amazon S3 EC2 instance. Postupujte podle kroků popsaných v dokumentaci k Amazon pro export Amazon EC2 instance, které chcete nainstalovat nástroj Amazon EC2 rozhraní příkazového řádku (CLI) a spusťte příkaz Vytvořit instanci export úkolů EC2 instance exportovat do souboru VHD. Nezapomeňte použít **virtuálního pevného disku** disku&#95;IMAGE&#95;formát proměnné při spuštění **vytvořit instanci export úkolů** příkaz. Exportovaný soubor virtuálního pevného disku je uložen v Amazon S3 sady, které určíte během tohoto procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Soubor VHD stáhněte ze sady S3. Pak vyberte příslušný soubor VHD **akce** > **Stáhnout**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Zkopírujte virtuální pevný disk z jiné mimo Azure cloud
Pokud migrujete virtuální pevný disk z jiných - cloudové úložiště Azure do Azure, musíte napřed exportovat virtuální pevný disk do místního adresáře. Zkopírujte dokončení zdrojovou cestu místnímu adresáři, kde je uložený virtuální pevný disk.

##### <a name="copy-a-vhd-from-on-premises"></a>Zkopírujte virtuální pevný disk z místní
Pokud migrujete virtuální pevný disk z místního prostředí, budete potřebovat úplný zdrojový cestu, kde je uložený virtuální pevný disk. Zdrojová cesta může být server umístění nebo sdílené složky.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvořit cíl pro svůj disk VHD
Vytvořte účet úložiště pro údržbu virtuální pevné disky. Při plánování kam se mají ukládat virtuální pevné disky, zvažte následující body:

* Cílový účet úložiště může být úložiště standard nebo premium, v závislosti na požadavcích vaší aplikace.
* Oblast účtu úložiště musí být stejná jako úložiště Premium podporuje virtuální počítače Azure se vytvoří v konečné fázi. Může zkopírovat nového účtu úložiště, nebo se chystáte použít stejný účet úložiště na základě potřeb.
* Zkopírujte a uložte klíč účtu úložiště cílový účet úložiště pro další fáze.

Důrazně doporučujeme přesouvání všech dat pro produkční pracovního vytížení k používání služby storage úrovně premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Nahrání virtuálního pevného disku do úložiště Azure
Nyní když máte vaši virtuálního pevného disku v místním adresáři, můžete AzCopy nebo AzurePowerShell nahrát soubor VHD do služby Azure Storage. Obě možnosti jsou k dispozici zde:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Možnost 1: Použití Azure PowerShell Add-AzureVhd nahrát soubor VHD

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Příklad <Uri> může být ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Příklad <FileInfo> může být ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Možnost 2: Nahrání souboru VHD pomocí nástroje AzCopy
Pomocí AzCopy, můžete snadno nahrávat VHD přes Internet. V závislosti na velikosti virtuálních pevných disků může to trvat čas. Mějte na paměti, zkontrolujte vstupní/výstupní limity účtu úložiště při použití této možnosti. V tématu [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md) podrobnosti.

1. Stáhněte a nainstalujte AzCopy odsud: [nejnovější verzi AzCopy](http://aka.ms/downloadazcopy)
2. Otevřete prostředí Azure PowerShell a přejděte do složky, kde je nainstalován nástroj AzCopy.
3. Pomocí následujícího příkazu zkopírujte soubor virtuálního pevného disku z "Zdroje" do "Cíle".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Příklad:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Zde je uveden popis použité v příkazu AzCopy parametry:

   * **/ Zdroj:  *&lt;zdroj&gt;:***  umístění složky nebo URL adresa kontejneru úložiště, který obsahuje virtuální pevný disk.
   * **/ SourceKey:  *&lt;klíč účtu zdroj&gt;:***  klíč účtu úložiště zdrojového účtu úložiště.
   * **/ Cíle:  *&lt;cílové&gt;:***  úložiště URL adresa kontejneru VHD, který chcete zkopírovat.
   * **/ DestKey:  *&lt;klíč účtu cíle&gt;:***  klíč účtu úložiště cílový účet úložiště.
   * **/ BlobType: stránka:** Určuje, že cíl je objekt blob stránky.
   * **/ Vzor:  *&lt;název souboru&gt;:***  zadejte název souboru virtuálního pevného disku ke kopírování.

Podrobnosti o použití nástroje AzCopy nástroje najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti pro odesílání virtuálního pevného disku
Můžete také nahrát virtuální pevný disk na váš účet úložiště pomocí jedné z těchto způsobů:

* [Objekt Blob služby Azure Storage kopie rozhraní API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Objektů BLOB Azure Storage Explorer odesílání](https://azurestorageexplorer.codeplex.com/)
* [Referenční dokumentace rozhraní API úložiště importu/exportu služby REST](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Doporučujeme používat službu Import/Export, pokud je předpokládaná doba odesílání doba je delší než 7 dní. Můžete použít [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) k zjištění přibližné hodnoty času z jednotky velikost a přenášet data.
>
> Import a Export lze použít pro kopírování na standardní účet úložiště. Musíte zkopírovat z úložiště standard storage do prémiový účet úložiště pomocí nástroje, například AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Vytvořit virtuální počítače Azure pomocí služby Storage úrovně Premium
Po virtuálního pevného disku se nahrál nebo zkopírovat do požadovaný účet úložiště, postupujte podle pokynů v této části k registraci virtuálního pevného disku jako bitovou kopii operačního systému, nebo disk operačního systému v závislosti na vašem scénáři a pak vytvořte instanci virtuálního počítače z něj. Datový disk VHD lze připojit k virtuálnímu počítači po jeho vytvoření.
Ukázkový skript migrace je k dispozici na konci této části. Tento jednoduchý skript neodpovídá všechny scénáře. Musíte aktualizovat skript tak, aby odpovídaly s konkrétní scénář. Pokud chcete zobrazit, pokud se tento skript platí pro váš scénář, najdete níže [A ukázkový migrace skript](#a-sample-migration-script).

### <a name="checklist"></a>Kontrolní seznam
1. Počkat, až všechny disky VHD kopírování je dokončena.
2. Ujistěte se, že úložiště Premium je k dispozici v oblast, kterou provádíte migraci do.
3. Rozhodněte, nové řadu virtuálních počítačů, kterou budete používat. Měla by být schopný úložiště Premium a velikost by měla být v závislosti na dostupnosti v oblasti a na základě potřeb.
4. Rozhodněte, přesný velikost virtuálního počítače, které chcete použít. Velikost virtuálního počítače musí být dostatečně velký pro podporu většímu počtu datových disků, které máte. Například Pokud máte 4 datových disků, musí mít virtuální počítač 2 nebo více jader. Zvažte také výpočetní výkon, paměti a šířky pásma sítě musí.
5. Vytvořte účet úložiště Premium cílová oblast. Toto je účet, který budete používat pro nový virtuální počítač.
6. Máte aktuální podrobnosti o virtuálních počítačů, které jsou užitečné, včetně seznamu disků a odpovídající BLOB VHD.

Připravte aplikaci výpadek. K provedení čisté migrace, budete muset zastavit veškeré zpracování v aktuálním systému. Pak můžete získat do konzistentního stavu, které můžete migrovat na nové platformě. Doba trvání výpadku závisí na množství dat na discích k migraci.

> [!NOTE]
> Pokud vytváříte virtuální počítač Azure Resource Manager z specializované disku VHD, získáte informace [této šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) pro nasazení Resource Manager virtuálního počítače pomocí stávající disk.
>
>

### <a name="register-your-vhd"></a>Zaregistrovat svůj disk VHD
Vytvoření virtuálního počítače z virtuálního pevného disku operačního systému nebo na nový virtuální počítač připojit datový disk, je nutné je zaregistrovat. Postupujte podle kroků v závislosti na scénáři svůj disk VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Zobecněný virtuální pevný disk operačního systému vytvořit více instancí virtuálního počítače Azure
Po odeslání image OS zobecněný virtuální pevný disk k účtu úložiště Zaregistrujte ho jako **Image virtuálního počítače Azure** tak, aby z ní můžete vytvořit jeden nebo více instancí virtuálního počítače. Pomocí následující rutiny prostředí PowerShell zaregistrovat svůj disk VHD jako image operačního systému virtuálního počítače Azure. Zadejte adresu URL dokončení kontejneru, kde virtuálního pevného disku byla zkopírována do.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Zkopírujte a uložte název tuto novou bitovou kopii virtuálního počítače Azure. V předchozím příkladu je *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečný operačního systému virtuálního pevného disku pro vytvoření jedné instance virtuálního počítače Azure
Po odeslání jedinečný virtuální pevný disk operačního systému k účtu úložiště Zaregistrujte ho jako **Disk s operačním systémem Azure** tak, aby instance virtuálního počítače můžete vytvořit z něj. Použijte tyto rutiny prostředí PowerShell k registraci svůj disk VHD jako Disk operačního systému Azure. Zadejte adresu URL dokončení kontejneru, kde virtuálního pevného disku byla zkopírována do.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Zkopírujte a uložte název tento nový Disk operačního systému Azure. V předchozím příkladu je *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Data na disku VHD, který chcete připojit k nové instance virtuálního počítače Azure
Po odeslání datový disk VHD k účtu úložiště zaregistrujte jako datový Disk Azure tak, aby se lze připojit k vaší nové DS Series, DSv2 series nebo GS virtuálního počítače Azure řady instance.

Použijte tyto rutiny prostředí PowerShell k registraci vaší virtuální pevný disk jako datový Disk Azure. Zadejte adresu URL dokončení kontejneru, kde virtuálního pevného disku byla zkopírována do.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Zkopírujte a uložte název tento nový datový Disk Azure. V předchozím příkladu je *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Vytvoření virtuálního počítače s podporou služby Storage úrovně Premium
Jednou image operačního systému nebo disk operačního systému jsou zaregistrovány, vytvořte nový DS-series, DSv2-series nebo GS-series virtuálních počítačů. Budete používat image operačního systému nebo název disku operačního systému, který je zaregistrovaný. Vyberte typ virtuálního počítače z vrstvy úložiště Premium. V následujícím příkladu se používá *Standard_DS2* velikost virtuálního počítače.

> [!NOTE]
> Velikost disku a ujistěte se, že odpovídá požadavkům na výkon a velikosti disku Azure a vaše kapacita aktualizujte.
>
>

Použijte rutiny Powershellu krok za krokem níže k vytvoření nového virtuálního počítače. Nastavte nejprve, běžné parametry:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Nejprve vytvořte Cloudová služba, ve kterém bude hostitelem nové virtuální počítače.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Dále vytvořte instanci virtuálního počítače Azure z bitové kopie operačního systému nebo Disk operačního systému, který je zaregistrovaný v závislosti na vašem scénáři.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Zobecněný virtuální pevný disk operačního systému vytvořit více instancí virtuálního počítače Azure
Vytvořte jeden nebo více nový virtuální počítač Azure řady DS instance pomocí **bitovou kopii operačního systému služby Azure** který je zaregistrovaný. Při vytváření nového virtuálního počítače, jak je uvedeno níže, zadejte název této bitové kopie operačního systému v konfiguraci virtuálního počítače.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečný operačního systému virtuálního pevného disku pro vytvoření jedné instance virtuálního počítače Azure
Vytvoření nové služby DS řady virtuálního počítače Azure instance pomocí **Disk s operačním systémem Azure** který je zaregistrovaný. Při vytváření nového virtuálního počítače, jak je uvedeno níže, zadejte tento název disku operačního systému v konfiguraci virtuálního počítače.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Zadejte další virtuální počítač Azure informace, například cloudové služby, oblast, účet úložiště, skupinu dostupnosti a zásady ukládání do mezipaměti. Všimněte si, že instance virtuálního počítače musí být umístěné společně se službou přidružené operační systém nebo datové disky, takže účet vybranou cloudovou službu, oblast a úložiště musí být ve stejném umístění jako základní virtuální pevné disky, a těchto disků.

### <a name="attach-data-disk"></a>Připojení datového disku
Nakonec pokud jste si zaregistrovali datový disk VHD, připojte je k nové úložiště Premium podporující virtuálního počítače Azure.

Pomocí následující rutiny prostředí PowerShell připojit datový disk do nového virtuálního počítače a zadejte zásady ukládání do mezipaměti. V následujícím příkladu je ukládání do mezipaměti zásada nastavená *jen pro čtení*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Mohou existovat další kroky nezbytné pro podporu vaší aplikace, která je nesmí být zahrnuté v této příručce.
>
>

### <a name="checking-and-plan-backup"></a>Kontrola a plánování zálohování
Jakmile nový virtuální počítač je v provozu, přístup pomocí stejné přihlašovacího id a heslo je jako původní virtuální počítač a ověřte, že vše funguje podle očekávání. Nový virtuální počítač bude obsahovat všechna nastavení, včetně prokládané svazky.

Posledním krokem je plánování zálohování a plán údržby pro nový virtuální počítač podle potřeb aplikace.

### <a name="a-sample-migration-script"></a>Ukázkový skript migrace
Pokud máte víc virtuálních počítačů k migraci, bude užitečné automatizace prostřednictvím skriptů prostředí PowerShell. Následuje ukázkový skript, který zautomatizuje migrace virtuálního počítače. Poznámka: níže uvedený skript se pouze příklad a neexistují několik předpokladům o aktuální disky virtuálních počítačů. Musíte aktualizovat skript tak, aby odpovídaly s konkrétní scénář.

Předpoklady jsou:

* Vytváříte klasické virtuální počítače Azure.
* Vaše zdrojové disky operačního systému a datové disky zdroje jsou ve stejném účtu úložiště a kontejneru. Pokud vaše disky operačního systému a datové disky nejsou na stejném místě, můžete použít ke kopírování disků VHD přes účty úložiště a kontejnery AzCopy nebo Azure PowerShell. Najdete v předchozím kroku: [kopie virtuálního pevného disku s AzCopy nebo prostředí PowerShell](#copy-vhd-with-azcopy-or-powershell). Úpravy tento skript ke splnění váš scénář je další možnost, ale doporučujeme použít AzCopy nebo prostředí PowerShell, protože je jednodušší a rychlejší.

Skriptu pro automatizaci najdete níže. Nahradí text s informacemi a skript tak, aby odpovídaly s konkrétní scénář aktualizovat.

> [!NOTE]
> Pomocí stávající skript nezachovává síťovou konfiguraci vašeho zdrojového virtuálního počítače. Musíte opětovná konfigurace nastavení sítě na migrovaných virtuálních počítačů.
>
>

```
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
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

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

    # how frequently to report the copy status in sceconds
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
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
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
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
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

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
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
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
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
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
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
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
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
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
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

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optimalizace
Aktuální konfigurace virtuálních počítačů lze přizpůsobit určený speciálně pro funkce fungují dobře u standardní disky. Například pokud chcete zvýšit výkon pomocí mnoho disků v prokládaný svazek. Například místo použití 4 disky samostatně na Storage úrovně Premium, možná nebudete moct optimalizovat náklady tak, že jeden disk. Optimalizace jako tento potřeba zpracovávat případ od případu a vyžadují vlastní kroky po migraci. Všimněte si také, že tento proces nemusí fungovat i pro databáze a aplikace, které závisí na definované v nastavení rozložení disku.

##### <a name="preparation"></a>Příprava
1. Dokončení migrace jednoduchý, jak je popsáno v předchozí části. Optimalizace se provede na nový virtuální počítač po dokončení migrace.
2. Zadejte nové velikosti disků, které jsou potřebné pro konfiguraci optimalizované.
3. Určí, mapování na nový disk specifikace aktuální disky nebo svazky.

##### <a name="execution-steps"></a>Provedení kroků
1. Vytvořte nový disků se správnými velikostmi ve virtuálním počítači úložiště Premium.
2. Přihlášení k virtuálních počítačů a zkopírujte data z aktuální svazek na nový disk, který se mapuje na tomto svazku. To lze proveďte pro všechny aktuální svazky, které potřebují k mapování na nový disk.
3. V dalším kroku změnit nastavení aplikace, přejděte na nové disky a odpojit starých svazků.

Ladění aplikace pro lepší výkon disku, naleznete v [optimalizace výkonu aplikace](../../virtual-machines/windows/premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrace aplikací
Databáze a jiné komplexní aplikace může vyžadovat zvláštní postup podle definice poskytovatele aplikace pro migraci. Naleznete v dokumentaci k příslušné aplikace. Například Obvykle můžete migrovat databází pomocí zálohování a obnovení.

## <a name="next-steps"></a>Další postup
Najdete v následujících materiálech u konkrétních scénářů pro migraci virtuálních počítačů:

* [Migrovat virtuální počítače, které jsou mezi účty úložiště Azure](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Vytvoření a nahrání virtuálního pevného disku serveru Windows do Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření a nahrání virtuálního pevného disku Linux do Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace virtuálních počítačů z Amazon AWS k Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zkontrolujte také, další informace o Azure Storage a virtuální počítače Azure v následujících zdrojích:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Storage úrovně Premium: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../virtual-machines/windows/premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
