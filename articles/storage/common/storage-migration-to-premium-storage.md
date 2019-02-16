---
title: Migrace virtuálních počítačů na Azure Premium Storage | Dokumentace Microsoftu
description: Migrujte vaše stávající virtuální počítače na Azure Premium Storage. Premium Storage nabízí podporu vysoce výkonných disků s nízkou latencí pro úlohy můžu vstupně-výstupními operacemi na Azure Virtual Machines.
services: storage
author: yuemlu
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.subservice: common
ms.openlocfilehash: bb88bf7ddaa93336c812b1ddc9794dad8daa64b7
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330575"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrace na Azure Premium Storage (nespravované disky)

> [!NOTE]
> Tento článek popisuje, jak migrovat virtuální počítač, který používá nespravované disky standard k virtuálnímu počítači, který používá disky úrovně premium. Doporučujeme použít Azure Managed Disks pro nové virtuální počítače a další, abyste převedli výše uvedené nespravované disky na managed disks. Spravované disky popisovač základní účty úložiště, není nutné. Další informace najdete v tématu naše [Přehled služby Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače provozované můžu intenzivních vstupně-výstupních operací. Migrací vaší aplikace disky virtuálních počítačů na Azure Premium Storage můžete využít výhod rychlost a výkon těchto disků.

Účel tohoto průvodce je pomoci nové uživatele služby Azure Premium Storage lepší Příprava hladkým přechodem z jejich aktuálního systému na Premium Storage. Průvodce řeší tři klíčové komponenty v tomto procesu:

* [Plánování migrace na Premium Storage](#plan-the-migration-to-premium-storage)
* [Příprava a zkopírovat virtuální pevné disky (VHD) na Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Vytvoření virtuálního počítače Azure pomocí služby Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Můžete migrovat virtuální počítače z jiných platforem na Azure Premium Storage, nebo migrovat stávající virtuální počítače Azure ze služby Storage úrovně Standard na Premium Storage. Tento průvodce popisuje kroky pro oba dva scénáře. Postupujte podle kroků uvedených v příslušné části v závislosti na vašem scénáři.

> [!NOTE]
> Najdete přehled funkcí a ceny premium SSD: [Vyberte typ disku pro virtuální počítače IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). Doporučujeme migrovat všechny disku virtuálního počítače vyžadující vysokou vstupně-výstupních operací na Azure Premium Storage pro zajištění nejlepšího výkonu pro vaši aplikaci. Pokud na disku nevyžaduje vysoké IOPS, můžete omezit náklady udržováním ve standardním úložišti, který ukládá data na disku virtuálního počítače na jednotky pevných disků (HDD) namísto jednotky SSD.
>

Dokončení procesu migrace v celém rozsahu může vyžadovat další akce před a po provedení kroků v tomto průvodci k dispozici. Mezi příklady patří konfigurace virtuálních sítí nebo koncovými body a provádění změn kódu v rámci vlastní aplikace může být nutné výpadkům ve vaší aplikaci. Tyto akce jsou jedinečné pro jednotlivé aplikace a byste měli provést spolu s kroky popsané v této příručce k úplné přechodu na Premium Storage jako bezproblémové co nejvíc.

## <a name="plan-the-migration-to-premium-storage"></a>Plánování migrace na Premium Storage
Tato část zajišťuje připraveni dodržovat postupy migrace popsané v tomto článku a vám umožní nejlepší rozhodnutí o typech virtuálních počítačů a Disk.

### <a name="prerequisites"></a>Požadavky
* Budete potřebovat předplatné Azure. Pokud ho nemáte, můžete vytvořit jeden měsíc [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) předplatné nebo navštivte [ceny za Azure](https://azure.microsoft.com/pricing/) další možnosti.
* Spouštění rutin Powershellu, musíte modul Microsoft Azure PowerShell. Bod instalace a pokyny pro instalaci jsou popsané v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
* Když plánujete použít virtuální počítače Azure běží na Premium Storage, budete muset použít virtuální počítače podporující Premium Storage. Disky Standard a Premium Storage můžete použít s podporou virtuálních počítačů služby Premium Storage. Disky storage úrovně Premium bude k dispozici s více typy virtuálních počítačů v budoucnu. Další informace o všech dostupných typů disku virtuálního počítače Azure a velikostí najdete v tématu [velikosti virtuálních počítačů](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [velikosti pro Cloud Services](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Požadavky
Virtuální počítač Azure podporuje připojení několik disků Premium Storage tak, aby vaše aplikace může mít až 256 TB úložiště na každý virtuální počítač. Díky službě Premium Storage vaše aplikace dosáhnout 80 000 IOPS (vstupně výstupní operace za sekundu) na virtuálním počítači a 2 000 MB za druhé propustnost disků jednotlivých virtuálních počítačů s extrémně nízkými latencemi pro operace čtení. Máte možnost v různých virtuálních počítačů a disků. Tato část se dozvíte, jak najít možnost, která nejlépe vyhovuje vaší úlohy.

#### <a name="vm-sizes"></a>Velikost virtuálních počítačů
Specifikace velikosti virtuálního počítače Azure jsou uvedeny v [velikosti virtuálních počítačů](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Zkontrolujte charakteristiky výkonu virtuálních počítačů, které pracují se službou Premium Storage a zvolte nejvhodnější velikosti virtuálního počítače, který nejlépe vyhovuje vaší úlohy. Ujistěte se, že je dostatečná šířka pásma dostupné na virtuálním počítači Centrum umožňující prosazovat diskové přenosy.

#### <a name="disk-sizes"></a>Velikost disků
Existuje pět typů disků, které lze použít s virtuálním Počítačem a obsahují konkrétní IOPs a propustnost omezení. Vzít v úvahu tyto limity, když vyberete typ disku pro virtuální počítač podle potřeb vaší aplikace z hlediska kapacity, výkonu, škálovatelnosti a načte ve špičce.

| Typ disky Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Velikost disku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Vstupně-výstupní operace za sekundu / disk       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Propustnost / disk | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

V závislosti na velikosti pracovní zátěže určí, jestli další datové disky jsou nezbytné pro váš virtuální počítač. K virtuálnímu počítači můžete připojit několik trvalých datových disků. V případě potřeby můžete prokládanou discích zvýšit kapacitu a výkon svazku. (Podívejte se, co je prokládání disků [tady](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Pokud prokládanou datových disků Premium Storage pomocí [prostory úložiště][4], měli byste ji nakonfigurovat s jedním sloupcem pro každý disk, který se používá. Celkový výkon prokládaných svazků v opačném případě může být nižší než očekávané kvůli nerovnoměrné distribuci provozu discích. Pro virtuální počítače s Linuxem můžete použít *mdadm* nástroj to samé. Najdete v článku [konfigurace softwaru diskového pole RAID v Linuxu](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobnosti.

#### <a name="storage-account-scalability-targets"></a>Cíle škálovatelnosti účtu úložiště
Účty úložiště úrovně Premium mají následující cíle škálovatelnosti kromě [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md). Požadavcích aplikace překročení cíle škálovatelnosti z jednoho účtu úložiště, sestavení aplikace pro použití více účtů úložiště a data rozdělit mezi tyto účty úložiště.

| Celkový počet účtů kapacity | Celková šířka pásma pro účet místně redundantního úložiště |
|:--- |:--- |
| Kapacita disku: 35TB<br />Kapacita snímku: 10 TB |Až na 50 gigabity za sekundu pro příchozí a odchozí |

Další informace o specifikacích Premium Storage, podívejte se na [škálovatelnost a výkonnostní cíle Azure Storage](storage-scalability-targets.md#premium-storage-account-scale-limits).

#### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku
Ve výchozím nastavení, disk zásady ukládání do mezipaměti je *jen pro čtení* pro všechny úrovně Premium datové disky, a *čtení a zápis* pro disk s operačním systémem Premium připojené k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro zajištění optimálního výkonu pro vaše aplikace IOs. Náročné na zápis nebo jen pro zápis datové disky (jako jsou například soubory protokolu serveru SQL Server) Zakázání používání mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace. Nastavení mezipaměti pro existující datové disky můžete aktualizovat pomocí [webu Azure Portal](https://portal.azure.com) nebo *- HostCaching* parametr *Set-AzureDataDisk* rutiny.

#### <a name="location"></a>Umístění
Vyberte umístění, kde je k dispozici Azure Premium Storage. Zobrazit [služeb Azure podle oblasti](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění. Virtuální počítače umístěné ve stejné oblasti jako účet úložiště, že úložiště disky virtuálního počítače získáte mnohem lepší výkon než pokud jsou v oblastech.

#### <a name="other-azure-vm-configuration-settings"></a>Další nastavení konfigurace virtuálního počítače Azure
Při vytváření virtuálního počítače Azure, zobrazí výzva ke konfiguraci určitá nastavení virtuálního počítače. Nezapomeňte, že některá nastavení jsou opravené životního cyklu virtuálního počítače, zatímco můžete upravit nebo přidat další později. Zkontrolujte nastavení konfigurace těchto virtuálních počítačů Azure a ujistěte se, že tyto jsou nakonfigurována tak, aby odpovídaly požadavkům vaší úlohy.

### <a name="optimization"></a>Optimalizace
[Azure Premium Storage: Návrh pro vysoký výkon](../../virtual-machines/windows/premium-storage-performance.md) poskytuje pokyny pro vytváření vysoce výkonné aplikace využívající Azure Premium Storage. Můžete použít pokyny v kombinaci s osvědčené postupy z hlediska výkonu pro technologie, které používá vaše aplikace.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Příprava a zkopírovat virtuální pevné disky (VHD) na Premium Storage
Následující část obsahuje pokyny pro přípravu virtuálních pevných disků ze svého virtuálního počítače a kopírování virtuálních pevných disků do služby Azure Storage.

* [Scénář 1: "Existujících virtuálních počítačů Azure migruji do služby Azure Premium Storage."](#scenario1)
* [Scénář 2: "Migruji virtuálních počítačů z jiných platforem na Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Požadavky
Virtuální pevné disky přípravy na migraci, budete potřebovat:

* Předplatné Azure, účet úložiště a kontejner v tomto účtu úložiště, do kterého můžete zkopírovat virtuální pevné disky. Všimněte si, že cílový účet úložiště může být účet Standard nebo Premium Storage podle potřeby.
* Nástroj pro zobecnit virtuální pevný disk, pokud máte v úmyslu vytvořit několik instancí virtuálních počítačů z něj. Například nástroje sysprep pro Windows nebo virt-sysprep pro Ubuntu.
* Nástroj pro nahrání souboru VHD do účtu úložiště. Zobrazit [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md) nebo použijte [Průzkumníka služby Azure storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Tato příručka popisuje kopírování virtuální pevný disk pomocí nástroje AzCopy.

> [!NOTE]
> Pokud zvolíte možnost synchronní kopie pomocí nástroje AzCopy, pro zajištění optimálního výkonu, zkopírujte virtuální pevný disk spuštěním jednoho z těchto nástrojů z virtuálního počítače Azure, který je ve stejné oblasti jako cílový účet úložiště. Pokud kopírujete virtuální pevný disk z virtuálního počítače Azure do jiné oblasti, může být pomalejší výkon.
>
> Kopírování velkých objemů dat přes omezenou šířkou pásma, vezměte v úvahu [posílat data do úložiště objektů Blob pomocí služby Azure Import/Export](../storage-import-export-service.md); to umožňuje přenos dat přenosem pevných disků do datacentra Azure. Služba Azure Import/Export můžete použít ke zkopírování dat na účet úložiště úrovně standard. Jakmile jsou data ve vašem účtu úložiště úrovně standard, můžete použít buď [kopírování objektů Blob rozhraní API](https://msdn.microsoft.com/library/azure/dd894037.aspx) nebo AzCopy k přenosu dat do účtu premium storage.
>
> Všimněte si, že Microsoft Azure podporuje pouze soubory virtuálního pevného disku pevné velikosti. Soubory VHDX nebo dynamický virtuální pevné disky nejsou podporovány. Pokud máte dynamický virtuální pevný disk, můžete ji převést na pevné velikosti pomocí [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) rutiny.
>
>

### <a name="scenario1"></a>Scénář 1: "Existujících virtuálních počítačů Azure migruji do služby Azure Premium Storage."
Pokud migrujete existující virtuální počítače Azure, zastavte virtuální počítač, virtuální pevné disky podle typu virtuálního pevného disku, které chcete připravit a pak zkopírujte virtuální pevný disk pomocí nástroje AzCopy nebo prostředí PowerShell.

Virtuální počítač musí být úplně dolů k migraci do čistého stavu. Bude výpadek až po dokončení migrace.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Příprava VHD pro migraci
Pokud migrujete existující virtuální počítače Azure do služby Premium Storage, může být virtuální pevný disk:

* Bitové kopie operačního systému
* Disk jedinečné operačního systému
* Datový disk

Níže najdete provedeme tyto 3 scénáře pro přípravu vašeho virtuálního pevného disku.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Vytvoření víc instancí virtuálních počítačů pomocí generalizovaného virtuálního pevného disku operačního systému
Pokud nahráváte virtuálního pevného disku, který se použije k vytvoření několika obecných instancí virtuálních počítačů Azure, musíte nejprve zobecnit virtuální pevný disk pomocí nástroje sysprep. To se vztahuje na virtuální pevný disk, který je místně nebo v cloudu. Nástroj Sysprep odebere všechny informace specifické pro počítač z virtuálního pevného disku.

> [!IMPORTANT]
> Pořízení snímku nebo zálohování virtuálního počítače před že ji zobecníte. Spuštění sysprep se zastavit a uvolnit instanci virtuálního počítače. Postupujte podle kroků níže nástroje Sysprep virtuálního pevného disku operačního systému Windows. Všimněte si, že spuštění příkazu Sysprep bude vyžadovat, abyste vypnout virtuální počítač. Další informace o nástroji Sysprep najdete v tématu [přehled nástroje Sysprep](https://technet.microsoft.com/library/hh825209.aspx) nebo [technické informace o nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otevřete okno příkazového řádku jako správce.
2. Zadejte následující příkaz, chcete-li spustit nástroj Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. V nástroji pro přípravu systému, vyberte prostředí systému zadejte Out-of-Box (OOBE), zaškrtněte políčko generalizace, vyberte **vypnutí**a potom klikněte na tlačítko **OK**, jak je znázorněno na následujícím obrázku. Nástroj Sysprep zobecní operační systém, který se vypnutí systému.

    ![][1]

Pro virtuální počítač s Ubuntu použijte to samé virt nástroje sysprep. Zobrazit [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) další podrobnosti. Viz také některé z oblasti open source [zřizování serveru Linux softwaru](http://www.cyberciti.biz/tips/server-provisioning-software.html) u ostatních operačních systémů Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Použijte jedinečné virtuální pevný disk operačního systému k vytvoření jedné instance virtuálního počítače
Pokud máte aplikaci spuštěné na virtuálním počítači, který vyžaduje data konkrétního počítače, ne zobecnit virtuální pevný disk. Zobecněný virtuální pevný disk je možné vytvořit instanci virtuálního počítače Azure jedinečný. Například pokud máte řadiče domény na virtuální pevný disk, provádění programu sysprep filtrovacího řetězce se neúčinná jako řadič domény. Projděte si aplikace spuštěné na virtuálním počítači a dopad na nich spuštěné nástroje sysprep před zobecňuje se virtuální pevný disk.

##### <a name="register-data-disk-vhd"></a>Registrovat datový virtuální pevný disk
Pokud máte v Azure k migraci datových disků, musí se zkontrolujte, zda se vypnout virtuální počítače, které používají tyto datové disky.

Postupujte podle kroků popsaných níže a zkopírujte virtuální pevný disk do Azure Storage úrovně Premium a zaregistrujte ho jako zřízené datový disk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvořit cíl pro vašeho virtuálního pevného disku
Vytvoření účtu úložiště pro zachování virtuálních pevných disků. Při plánování, kam se mají ukládat vaše virtuální pevné disky, zvažte následující body:

* Cílový účet úložiště úrovně Premium.
* Umístění účtu úložiště musí být stejný jako Premium Storage podporuje virtuální počítače Azure se vytvoří v konečné fázi. Můžete zkopírovat do nového účtu úložiště, nebo chcete použít stejný účet úložiště podle vašich potřeb.
* Zkopírujte a uložte klíč účtu úložiště účtu cílového úložiště pro další fázi.

Pro datové disky je možné zachovat některé datové disky v účtu úložiště úrovně standard (například disky, které obsahují chladnější úložiště), ale důrazně doporučujeme, abyste přesouvání všech dat pro produkční úlohy, které chcete používat premium storage.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3. Zkopírujte virtuální pevný disk pomocí nástroje AzCopy nebo prostředí PowerShell
Je potřeba najít váš kontejner cesty a klíč účtu úložiště zpracovat kteroukoli z těchto dvou možností. Kontejner cesty a klíč účtu úložiště najdete v **webu Azure Portal** > **úložiště**. Kontejner bude mít adresu URL jako "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Option 1: Zkopírujte virtuální pevný disk pomocí nástroje AzCopy (asynchronní kopie)
Pomocí AzCopy můžete snadno nahrávat VHD přes Internet. V závislosti na velikosti virtuálních pevných disků to může trvat dobu. Nezapomeňte zkontrolovat příchozí a odchozí přenos limity účtu úložiště při použití této možnosti. Zobrazit [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md) podrobnosti.

1. Stáhněte a nainstalujte nástroj AzCopy z tohoto: [Nejnovější verzi AzCopy](https://aka.ms/downloadazcopy)
2. Otevřete prostředí Azure PowerShell a přejděte do složky, ve kterém je nástroj AzCopy nainstalovaný.
3. Použijte následující příkaz pro kopírování souboru virtuálního pevného disku z "Zdroj" na "Cíl".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Příklad:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Tady jsou popisy parametrů použitých v příkazu AzCopy:

   * **/ Zdroj:  *&lt;zdroj&gt;:*** Umístění složky nebo adresa URL kontejneru úložiště, která obsahuje virtuální pevný disk.
   * **/SourceKey: *&lt;source-account-key&gt;:*** Klíč účtu úložiště účtu zdrojového úložiště.
   * **/ Dest:  *&lt;cílové&gt;:*** Adresa URL kontejneru úložiště VHD, který chcete zkopírovat.
   * **/ DestKey:  *&lt;klíč účtu dest&gt;:*** Klíč účtu úložiště účtu cílového úložiště.
   * **Nebo vzor, který:  *&lt;název souboru&gt;:*** Zadejte název souboru VHD, který chcete zkopírovat.

Podrobnosti o použití nástroje AzCopy nástroj, najdete v článku [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Option 2: Zkopírujte virtuální pevný disk pomocí Powershellu (Synchronized kopie)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Můžete také zkopírovat soubor virtuálního pevného disku pomocí Powershellové rutiny Start-AzStorageBlobCopy. Pomocí následujícího příkazu v prostředí Azure PowerShell ke kopírování virtuálního pevného disku. Hodnoty v <> nahraďte odpovídajícími hodnotami z vašeho zdrojového a cílového účtu úložiště. Pokud chcete použít tento příkaz, musíte mít kontejneru s názvem virtuální pevné disky v cílovém účtu úložiště. Pokud kontejner neexistuje, vytvořte jej před spuštěním příkazu.

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

### <a name="scenario2"></a>Scénář 2: "Migruji virtuálních počítačů z jiných platforem na Azure Premium Storage."
Pokud migrujete virtuální pevný disk z jiných - Azure Cloud Storage do Azure, musíte napřed exportovat virtuální pevný disk do místního adresáře. Úplný zdrojový cesty místnímu adresáři, kde je uložen virtuální pevný disk po ruce a pak pomocí nástroje AzCopy k nahrání do služby Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Exportovat virtuální pevný disk do místního adresáře
##### <a name="copy-a-vhd-from-aws"></a>Zkopírujte virtuální pevný disk z AWS
1. Pokud používáte AWS, exportujte EC2 instance do virtuální pevný disk v kontejneru Amazon S3. Postupujte podle postupu popsaného v dokumentaci k Amazon pro export Amazon EC2 instance instalace nástrojů rozhraní příkazového řádku (CLI) Amazon EC2 a spusťte příkaz Vytvořit instance-export úlohu exportovat EC2 instance do souboru virtuálního pevného disku. Nezapomeňte použít **virtuálního pevného disku** disku&#95;IMAGE&#95;formát proměnné při spuštění **vytvořit instance-export úlohu** příkazu. Exportovaný soubor virtuálního pevného disku se uloží do kbelíku Amazon S3, který jste zadali během tohoto procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Stáhněte si soubor virtuálního pevného disku z sady S3. Vyberte soubor virtuálního pevného disku, pak **akce** > **Stáhnout**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Zkopírujte virtuální pevný disk z jiných cloudu mimo Azure
Pokud migrujete virtuální pevný disk z jiných - Azure Cloud Storage do Azure, musíte napřed exportovat virtuální pevný disk do místního adresáře. Zkopírujte cestu úplný zdrojový místnímu adresáři, kde je uložen virtuální pevný disk.

##### <a name="copy-a-vhd-from-on-premises"></a>Zkopírujte virtuální pevný disk z on-premises
Pokud migrujete virtuální pevný disk z místního prostředí, budete potřebovat úplný zdrojový cestu, kde je virtuální pevný disk uložený. Zdrojová cesta může být soubor nebo umístění sdílené složky na serveru.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvořit cíl pro vašeho virtuálního pevného disku
Vytvoření účtu úložiště pro zachování virtuálních pevných disků. Při plánování, kam se mají ukládat vaše virtuální pevné disky, zvažte následující body:

* Cílový účet úložiště může být standard nebo premium storage podle požadavků vašich aplikací.
* Oblast účtu úložiště musí být stejný jako Premium Storage podporuje virtuální počítače Azure se vytvoří v konečné fázi. Můžete zkopírovat do nového účtu úložiště, nebo chcete použít stejný účet úložiště podle vašich potřeb.
* Zkopírujte a uložte klíč účtu úložiště účtu cílového úložiště pro další fázi.

Důrazně doporučujeme, abyste přesouvání všech dat pro produkční úlohy, které chcete používat premium storage.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Nahrání virtuálního pevného disku do úložiště Azure
Teď, když máte virtuální pevný disk v místním adresáři, můžete použít nástroj AzCopy nebo AzurePowerShell k nahrání souboru VHD do služby Azure Storage. Obě možnosti jsou k dispozici zde:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Option 1: Použití Azure PowerShell Add-AzureVhd pro nahrání souboru VHD

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Příklad <Uri> může být ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Příklad <FileInfo> může být ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Option 2: Použití AzCopy pro nahrání souboru VHD
Pomocí AzCopy můžete snadno nahrávat VHD přes Internet. V závislosti na velikosti virtuálních pevných disků to může trvat dobu. Nezapomeňte zkontrolovat příchozí a odchozí přenos limity účtu úložiště při použití této možnosti. Zobrazit [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md) podrobnosti.

1. Stáhněte a nainstalujte nástroj AzCopy z tohoto: [Nejnovější verzi AzCopy](https://aka.ms/downloadazcopy)
2. Otevřete prostředí Azure PowerShell a přejděte do složky, ve kterém je nástroj AzCopy nainstalovaný.
3. Použijte následující příkaz pro kopírování souboru virtuálního pevného disku z "Zdroj" na "Cíl".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Příklad:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Tady jsou popisy parametrů použitých v příkazu AzCopy:

   * **/ Zdroj:  *&lt;zdroj&gt;:*** Umístění složky nebo adresa URL kontejneru úložiště, která obsahuje virtuální pevný disk.
   * **/SourceKey: *&lt;source-account-key&gt;:*** Klíč účtu úložiště účtu zdrojového úložiště.
   * **/ Dest:  *&lt;cílové&gt;:*** Adresa URL kontejneru úložiště VHD, který chcete zkopírovat.
   * **/ DestKey:  *&lt;klíč účtu dest&gt;:*** Klíč účtu úložiště účtu cílového úložiště.
   * **/ BlobType: stránka:** Určuje, že cíl je objekt blob stránky.
   * **Nebo vzor, který:  *&lt;název souboru&gt;:*** Zadejte název souboru VHD, který chcete zkopírovat.

Podrobnosti o použití nástroje AzCopy nástroj, najdete v článku [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti pro nahrání virtuálního pevného disku
Můžete také nahrát VHD do účtu úložiště pomocí jedné z následujících způsobů:

* [Objekt Blob úložiště Azure kopírování rozhraní API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Objekty BLOB služby Azure Storage Explorer nahrávání](https://azurestorageexplorer.codeplex.com/)
* [Reference k rozhraní API REST úložiště importu/exportu služby](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Doporučujeme používat službu Import/Export, pokud odhadované nahrávání doba je delší než 7 dní. Můžete použít [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pro odhad doby z jednotky pro velikost a přenos dat.
>
> Import/Export je možné zkopírovat do účtu úložiště úrovně standard. Je potřeba zkopírovat ze standard storage do účtu služby premium storage pomocí některého nástroje, například AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Vytvoření virtuálních počítačů Azure pomocí služby Premium Storage
Po virtuální pevný disk je nahráli nebo zkopírují na požadovaný účet úložiště, postupujte podle pokynů v této části zaregistrujte virtuální pevný disk jako image operačního systému, nebo disk s operačním systémem v závislosti na vašem scénáři a pak vytvořte instance virtuálního počítače z něj. Datový virtuální pevný disk může být připojen k virtuálnímu počítači po jeho vytvoření.
Ukázkový skript migrace najdete na konci této části. Tento jednoduchý skript neodpovídá všechny scénáře. Budete muset aktualizovat skript tak, aby odpovídaly s váš konkrétní scénář. Pokud tento skript platí pro váš scénář, najdete v sekci níže [ukázkový skript migrace](#a-sample-migration-script).

### <a name="checklist"></a>Kontrolní seznam
1. Počkat, až všechny disky virtuálního pevného disku kopírování byla dokončena.
2. Zajistěte, aby že Premium Storage je dostupné v oblasti, kterou provádíte migraci do.
3. Rozhodněte, nových řadách virtuálních počítačů, které budete používat. Měla by být schopný služby Premium Storage a velikost by měla být v závislosti na dostupnosti v oblasti a na základě vašich potřeb.
4. Rozhodněte, přesnou velikost virtuálního počítače, které bude používat. Velikost virtuálního počítače musí být dostatečně velký, aby podporoval počet datových disků, které máte. Například Pokud máte 4 datové disky, virtuální počítač musí mít 2 nebo více jader. Zvažte také, výpočetní výkon, paměť a šířka pásma sítě potřebuje.
5. Vytvoření účtu služby Premium Storage v cílové oblasti. Jedná se o účet, který budete používat pro nový virtuální počítač.
6. Máte aktuální podrobnosti o virtuálním počítači, které jsou po ruce, včetně seznamu disků a odpovídající objekty BLOB VHD.

Příprava aplikace pro výpadek. Pokud chcete provést čistou migrace, je nutné zastavit veškeré zpracování v aktuálním systému. Teprve pak můžete získat do konzistentního stavu, které můžete migrovat na novou platformu. Doba trvání výpadku bude záviset na množství dat na discích, které chcete migrovat.

> [!NOTE]
> Pokud vytváříte virtuální počítač Azure Resource Manageru ze specializovaného disku virtuálního pevného disku, najdete [Tato šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) pro nasazení virtuálních počítačů Resource Manageru pomocí existujícího disku.
>
>

### <a name="register-your-vhd"></a>Zaregistrujte svůj virtuální pevný disk
Vytvoření virtuálního počítače z virtuálního pevného disku operačního systému nebo připojení datového disku k novému virtuálnímu počítači, je nutné je zaregistrovat. Postupujte podle kroků v závislosti na scénáři vašeho virtuálního pevného disku.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Zobecněný virtuální pevný disk operačního systému pro vytvoření víc instancí virtuálních počítačů Azure
Po nahrání image operačního systému zobecněný virtuální pevný disk do účtu úložiště, zaregistrujte ho jako **Image virtuálního počítače Azure** tak, aby z ní můžete vytvořit jednu nebo víc instancí virtuálních počítačů. Pomocí následujících rutin prostředí PowerShell registrovat virtuální pevné disky jako image operačního systému virtuálního počítače Azure. Zadejte adresu URL dokončení kontejneru, kam se zkopíroval virtuálního pevného disku do.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Zkopírujte a uložte název této nové Image virtuálního počítače Azure. V předchozím příkladu je *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečné operační systém virtuálního pevného disku vytvořit jednu instanci virtuálního počítače Azure
Po jedinečné virtuální pevný disk operačního systému se nahraje do účtu úložiště, zaregistrujte ho jako **Disk s operačním systémem Azure** tak, aby z ní můžete vytvořit instanci virtuálního počítače. Použijte tyto rutiny prostředí PowerShell k registraci vašeho virtuálního pevného disku jako Disk s operačním systémem Azure. Zadejte adresu URL dokončení kontejneru, kam se zkopíroval virtuálního pevného disku do.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Zkopírujte a uložte název tohoto nového disku s operačním systémem Azure. V předchozím příkladu je *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Datový disk VHD, který chcete připojit k nové instance virtuálních počítačů Azure
Po nahrání datového disku virtuálního pevného disku do účtu úložiště zaregistroval jako datový Disk Azure tak, aby jej bylo možné připojit k nové řady DS, DSv2 series nebo instance virtuálního počítače Azure řady GS.

Použijte tyto rutiny prostředí PowerShell k registraci vašeho virtuálního pevného disku jako disku Azure Data. Zadejte adresu URL dokončení kontejneru, kam se zkopíroval virtuálního pevného disku do.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Zkopírujte a uložte název tento nový datový Disk Azure. V předchozím příkladu je *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Vytvoření virtuálního počítače podporující Premium Storage
Jednou image operačního systému nebo disk s operačním systémem byly zaregistrovány, vytvořte nové řady DS, DSv2-series nebo GS-series virtuálních počítačů. Budete používat image operačního systému nebo název disku operačního systému, které jste zaregistrovali. Vyberte typ virtuálního počítače z úrovně Premium Storage. V následujícím příkladu se používá *Standard_DS2* velikost virtuálního počítače.

> [!NOTE]
> Aktualizujte velikost disku, abyste měli jistotu, že odpovídá požadavkům na výkon a velikosti disku Azure a vaší kapacity.
>
>

Pomocí rutiny Powershellu krok za krokem při vytváření nového virtuálního počítače. Nejprve nastavte běžné parametry:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Nejprve vytvořte cloudovou službu, ve kterém bude hostitelem nové virtuální počítače.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Dále vytvořte instanci virtuálního počítače Azure z Image operačního systému nebo Disk s operačním systémem, které jste zaregistrovali v závislosti na vašem scénáři.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Zobecněný virtuální pevný disk operačního systému pro vytvoření víc instancí virtuálních počítačů Azure
Vytvořte jeden nebo více nového virtuálního počítače Azure řady DS instance pomocí **Image operačního systému Azure** , které jste zaregistrovali. Zadejte název této Image operačního systému v konfiguraci virtuálních počítačů při vytváření nového virtuálního počítače, jak je znázorněno níže.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečné operační systém virtuálního pevného disku vytvořit jednu instanci virtuálního počítače Azure
Vytvoření nového DS řady virtuálních počítačů Azure instance **Disk s operačním systémem Azure** , které jste zaregistrovali. Zadejte název tohoto disku s operačním systémem v konfiguraci virtuálních počítačů při vytváření nového virtuálního počítače, jak je znázorněno níže.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Zadejte další informace virtuálního počítače Azure, jako jsou cloudové služby, oblasti, účet úložiště, dostupnosti a zásady ukládání do mezipaměti. Mějte na paměti, musí být instance virtuálního počítače společně s přidružené operační systém nebo datové disky, takže vybranou cloudovou službu, oblasti a účet úložiště musí být ve stejném umístění jako základní virtuální pevné disky tyto disky.

### <a name="attach-data-disk"></a>Připojení datového disku
A konečně Pokud zaregistrujete datový disk virtuální pevné disky připojily k nové služby Premium Storage podporuje virtuální počítač Azure.

Použijte následující rutinu prostředí PowerShell pro připojení datového disku k novému virtuálnímu počítači a určení zásady ukládání do mezipaměti. V následujícím příkladu je nastavena zásady ukládání do mezipaměti *jen pro čtení*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Můžou existovat další kroky nutné k podpoře vaší aplikace, která je jako nepokrytý této příručce.
>
>

### <a name="checking-and-plan-backup"></a>Kontrola a plánu zálohování
Jakmile nový virtuální počítač je v provozu, k němu přistupovat pomocí stejné přihlašovací id a heslo je jako původní virtuální počítač a ověřte, že všechno funguje podle očekávání. Nový virtuální počítač bude obsahovat všechna nastavení, včetně prokládané svazky.

Posledním krokem je plán zálohování a plán údržby pro nový virtuální počítač podle potřeb vaší aplikace.

### <a name="a-sample-migration-script"></a>Ukázkový skript migrace
Pokud máte více virtuálních počítačů k migraci, automatizace pomocí skriptů PowerShell budou užitečné. Tady je ukázkový skript, který automatizuje migraci virtuálního počítače. Všimněte si, který níže uvedený skript je jenom jako příklad a existuje několik předpokladů o aktuální disky virtuálních počítačů. Budete muset aktualizovat skript tak, aby odpovídaly s váš konkrétní scénář.

Předpokladů jsou:

* Vytváříte klasické virtuální počítače Azure.
* Zdrojové disky s operačním systémem a datové disky zdroje jsou ve stejném účtu úložiště a stejného kontejneru. Pokud disky s operačním systémem a datové disky nejsou na stejném místě, můžete použít AzCopy nebo prostředí Azure PowerShell mají zkopírovat virtuální pevné disky účtů úložiště a kontejnerů. Podívejte se na předchozí krok: [Zkopírujte virtuální pevný disk pomocí nástroje AzCopy nebo prostředí PowerShell](#copy-vhd-with-azcopy-or-powershell). Tento skript podle vašeho scénáře pro úpravy je jinou možnost, ale doporučujeme použít AzCopy nebo prostředí PowerShell, protože je jednodušší a rychlejší.

Automatizační skript jsou uvedeny níže. Nahradit text s informacemi a aktualizujte skript v tak, aby odpovídaly s váš konkrétní scénář.

> [!NOTE]
> Použití existujícího skriptu Nezachovávat hodnotu síťovou konfiguraci vašeho zdrojového virtuálního počítače. Je potřeba znovu – konfigurace nastavení sítě na migrovaných virtuálních počítačích.
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

#### <a name="optimization"></a>Optimalizace
Aktuální konfigurace virtuálního počítače lze přizpůsobit speciálně pro práci s disky Standard. Například pro zvýšení výkonu s využitím počet disků v prokládaných svazků. Například nemusíte používat samostatně 4 disky v Premium Storage, bude pravděpodobně možné optimalizovat náklady tím, že jeden disk. Optimalizace, jako jsou tyto potřeby zpracovat případ od případu a vyžadují vlastní kroky po dokončení migrace. Všimněte si také, že tento proces nemusí fungovat dobře pro databáze a aplikace, které jsou závislé na rozložení disků, které jsou definované v nastavení.

##### <a name="preparation"></a>Příprava
1. Jednoduchá migrace dokončí, jak je popsáno v předchozí části. Optimalizace se provede k novému virtuálnímu počítači po migraci.
2. Definujte nové velikosti disků, které jsou potřebné pro optimalizovanou konfiguraci.
3. Určení mapování aktuální disky nebo svazky, které chcete nový specifikace disku.

##### <a name="execution-steps"></a>Postup spuštění
1. Vytvořte nové disky, které se správnými velikostmi na virtuální počítač Premium Storage.
2. Přihlaste se k virtuálnímu počítači a kopírování dat z aktuálního svazku na nový disk, který se mapuje na tomto svazku. To lze proveďte pro všechny aktuální svazky, které je potřeba namapovat na nový disk.
3. V dalším kroku změnit nastavení aplikace pro přepnutí na jiné disky a odpojení starých svazků.

Ladění aplikací pro lepší výkon disků, naleznete v části Optimalizace výkonu aplikace z našich [návrh pro vysoký výkon](../../virtual-machines/windows/premium-storage-performance.md) článku.

### <a name="application-migrations"></a>Migrace aplikací
Databáze a další komplexní aplikace může vyžadovat speciální kroky, podle definice poskytovatele aplikací pro migraci. Najdete dokumentaci k příslušné aplikace. Například Obvykle je možné migrovat databází pomocí zálohování a obnovení.

## <a name="next-steps"></a>Další postup
Viz následující prostředky pro konkrétní scénáře pro migraci virtuálních počítačů:

* [Migrace virtuálních počítačů Azure mezi účty úložiště](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Vytvoření a nahrání virtuálního pevného disku Windows serverem do Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření a nahrání linuxového virtuálního pevného disku do Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace virtuálních počítačů z služeb Amazon AWS k Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Viz také následující prostředky pro další informace o Azure Storage a Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Vyberte typ disku pro virtuální počítače IaaS](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
