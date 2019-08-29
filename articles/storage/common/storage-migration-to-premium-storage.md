---
title: Migrují se virtuální počítače do Azure Premium Storage | Microsoft Docs
description: Migrujte stávající virtuální počítače do Azure Premium Storage. Premium Storage nabízí podporu vysoce výkonných disků s nízkou latencí pro úlohy náročné na vstupně-výstupní operace běžící na Azure Virtual Machines.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 1bf46240303d1f31cd09c1a2723e18d27d3ef789
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124689"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrace do Azure Premium Storage (nespravované disky)

> [!NOTE]
> Tento článek popisuje, jak migrovat virtuální počítač, který používá nespravované standardní disky, do virtuálního počítače, který používá nespravované disky Premium. Doporučujeme, abyste pro nové virtuální počítače používali Azure Managed Disks a převedli jste předchozí nespravované disky na spravované disky. Managed Disks zpracovávat zdrojové účty úložiště, takže je nemusíte mít. Další informace najdete v našem [přehledu Managed disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, na kterých běží úlohy náročné na vstupně-výstupní operace. Můžete využít rychlost a výkon těchto disků migrací disků virtuálních počítačů aplikace do Azure Premium Storage.

Účelem tohoto průvodce je pomáhat novým uživatelům Azure Premium Storage lépe připravit se na zajištění hladkého přechodu ze současného systému na Premium Storage. Příručka řeší tři klíčové komponenty v tomto procesu:

* [Plánování migrace na Premium Storage](#plan-the-migration-to-premium-storage)
* [Příprava a zkopírování virtuálních pevných disků (VHD) do Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Vytvoření virtuálního počítače Azure pomocí Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Můžete buď migrovat virtuální počítače z jiných platforem do Azure Premium Storage nebo migrovat stávající virtuální počítače Azure ze služby Storage úrovně Standard do Premium Storage. Tato příručka se zabývá postupy obou dvou scénářů. Postupujte podle kroků uvedených v příslušné části v závislosti na vašem scénáři.

> [!NOTE]
> Přehled funkcí a ceny SSD úrovně Premium najdete v těchto verzích: [Vyberte typ disku pro virtuální počítače s IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). Pro dosažení optimálního výkonu vaší aplikace doporučujeme migrovat všechny disky virtuálních počítačů vyžadující vysoké IOPS do Azure Premium Storage. Pokud disk nevyžaduje vysoké IOPS, můžete omezit náklady tím, že ho zachováte ve službě Storage úrovně Standard, což ukládá data disků virtuálního počítače na jednotky pevného disku (HDD) místo SSD.
>

Dokončení procesu migrace v celém rozsahu může vyžadovat další akce před i po krocích uvedených v této příručce. Příklady zahrnují konfiguraci virtuálních sítí nebo koncových bodů nebo provádění změn kódu v rámci samotné aplikace, což může vyžadovat určité výpadky aplikace. Tyto akce jsou pro každou aplikaci jedinečné a měli byste je vyplnit spolu s kroky uvedenými v této příručce, aby byl úplný přechod Premium Storage co nejrychlejší.

## <a name="plan-the-migration-to-premium-storage"></a>Plánování migrace na Premium Storage
V této části se doručí, že budete připraveni postupovat podle kroků migrace v tomto článku, a pomůže vám to, abyste mohli provést nejlepší rozhodnutí na typech virtuálních počítačů a disků.

### <a name="prerequisites"></a>Požadavky
* Budete potřebovat předplatné Azure. Pokud ho nemáte, můžete si vytvořit měsíční předplatné [bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/) nebo přejít na [ceny Azure](https://azure.microsoft.com/pricing/) , kde najdete další možnosti.
* Pokud chcete spustit rutiny prostředí PowerShell, budete potřebovat modul Microsoft Azure PowerShell. Bod instalace a pokyny pro instalaci jsou popsané v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
* Pokud plánujete používat virtuální počítače Azure běžící na Premium Storage, je nutné použít virtuální počítače podporující Premium Storage. Můžete použít standardní i Premium Storage disky s Premium Storage podporujícími virtuální počítače. Disky Premium Storage budou v budoucnu k dispozici pro další typy virtuálních počítačů. Další informace o všech dostupných typech a velikostech disků virtuálních počítačů Azure najdete v tématu [velikosti pro virtuální počítače](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [velikosti pro Cloud Services](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Požadavky
Virtuální počítač Azure podporuje připojení několika Premium Storage disků, takže vaše aplikace můžou mít až 256 TB úložného prostoru na jeden virtuální počítač. Díky Premium Storage můžou vaše aplikace dosahovat 80 000 vstupně-výstupních operací za sekundu na virtuální počítač a propustnosti disku 2000 MB za sekundu na jeden virtuální počítač s extrémně nízkými latencemi pro operace čtení. Máte možnosti pro celou řadu virtuálních počítačů a disků. V této části vám pomůžou najít možnost, která nejlépe vyhovuje vašim úlohám.

#### <a name="vm-sizes"></a>Velikost virtuálních počítačů
Specifikace velikosti virtuálního počítače Azure jsou uvedené v části [velikosti pro virtuální počítače](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Přečtěte si výkonnostní charakteristiky virtuálních počítačů, které pracují se Premium Storage a vyberte nejvhodnější velikost virtuálního počítače, která nejlépe vyhovuje vašim úlohám. Ujistěte se, že je ve vašem VIRTUÁLNÍm počítači k dispozici dostatečná šířka pásma, aby bylo možné disk provozovat.

#### <a name="disk-sizes"></a>Velikost disků
Existuje pět typů disků, které lze použít s VIRTUÁLNÍm počítačem a každý má konkrétní omezení pro IOPs a propustnost. Vezměte v úvahu tato omezení při výběru typu disku pro váš virtuální počítač na základě potřeb vaší aplikace z důvodu kapacity, výkonu, škálovatelnosti a zatížení ve špičce.

| Typ disků úrovně Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Velikost disku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Vstupně-výstupní operace za sekundu / disk       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Propustnost / disk | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

V závislosti na vašich úlohách Zjistěte, jestli jsou pro váš virtuální počítač potřeba další datové disky. K VIRTUÁLNÍmu počítači můžete připojit několik trvalých datových disků. V případě potřeby můžete proložením na disky zvýšit kapacitu a výkon svazku. (Další informace najdete v tématu co [](../../virtual-machines/windows/premium-storage-performance.md#disk-striping)je to disk Stripe.) Pokud Premium Storage datové disky pomocí [prostorů úložiště][4], měli byste je nakonfigurovat pomocí jednoho sloupce pro každý disk, který se používá. V opačném případě může být celkový výkon prokládaného svazku nižší, než se očekává kvůli nerovnoměrné distribuci provozu mezi disky. Pro virtuální počítače se systémem Linux můžete použít nástroj *mdadm* ke stejnému účelu. Podrobnosti najdete v článku [Konfigurace softwarového pole RAID v systému Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

#### <a name="storage-account-scalability-targets"></a>Cíle škálovatelnosti účtu úložiště
Kromě [cílů Azure Storage škálovatelnosti a výkonu](storage-scalability-targets.md)Premium Storage účty mají následující cíle škálovatelnosti. Pokud požadavky vaší aplikace překračují cíle škálovatelnosti jednoho účtu úložiště, sestavte aplikaci tak, aby používala více účtů úložiště, a vytvořte oddíly dat v rámci těchto účtů úložiště.

| Celková kapacita účtu | Celková šířka pásma pro místně redundantní účet úložiště |
|:--- |:--- |
| Kapacita disku: 35TB<br />Kapacita snímku: 10 TB |Až 50 gigabitů za sekundu pro příchozí i odchozí |

Další informace o Premium Storage specifikacích najdete v [Azure Storage škálovatelnost a výkonnostní cíle](storage-scalability-targets.md#premium-performance-storage-account-scale-limits).

#### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti disku
Ve výchozím nastavení jsou zásady ukládání do mezipaměti disku jen pro *čtení* pro všechny disky Premium data a *ke čtení i zápisu* pro disk s operačním systémem Premium připojené k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro dosažení optimálního výkonu pro IOs vaší aplikace. U datových disků, které jsou výhradně pro zápis nebo pouze pro zápis (například souborů protokolu SQL Server), zakažte ukládání do mezipaměti disku, abyste dosáhli lepšího výkonu aplikace. Nastavení mezipaměti pro existující datové disky se dá aktualizovat pomocí [Azure Portal](https://portal.azure.com) nebo parametru *-HostCaching* rutiny *set-AzureDataDisk* .

#### <a name="location"></a>Location
Vyberte umístění, kde je k dispozici Azure Premium Storage. Aktuální informace o dostupných umístěních najdete v tématu [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services) . Virtuální počítače umístěné ve stejné oblasti jako účet úložiště, který ukládá disky pro virtuální počítač, budou poskytovat mnohem lepší výkon, než kdyby byly v samostatných oblastech.

#### <a name="other-azure-vm-configuration-settings"></a>Další nastavení konfigurace virtuálního počítače Azure
Při vytváření virtuálního počítače Azure se zobrazí výzva ke konfiguraci určitých nastavení virtuálního počítače. Pamatujte si, že pro celou dobu života virtuálního počítače se opravilo několik nastavení, zatímco později můžete upravovat nebo přidávat další. Zkontrolujte tato nastavení konfigurace virtuálního počítače Azure a ujistěte se, že jsou správně nakonfigurované tak, aby odpovídaly vašim požadavkům na úlohy.

### <a name="optimization"></a>Optimalizace
[Azure Premium Storage: Návrh pro vysoký výkon](../../virtual-machines/windows/premium-storage-performance.md) poskytuje pokyny pro vytváření vysoce výkonných aplikací s využitím Azure Premium Storage. Můžete postupovat podle pokynů v kombinaci s osvědčenými postupy pro výkon, které platí pro technologie používané vaší aplikací.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Příprava a zkopírování virtuálních pevných disků (VHD) do Premium Storage
V následující části najdete pokyny k přípravě VHD z virtuálního počítače a zkopírování virtuálních pevných disků do Azure Storage.

* [Scénář 1: "Migruji jste stávající virtuální počítače Azure do Azure Premium Storage."](#scenario1)
* [Scénář 2: "Migruji jste virtuální počítače z jiných platforem do Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Požadavky
Pokud chcete připravit virtuální pevné disky pro migraci, budete potřebovat:

* Předplatné Azure, účet úložiště a kontejner v účtu úložiště, do kterého můžete zkopírovat virtuální pevný disk. Všimněte si, že cílový účet úložiště může být účtem Standard nebo Premium Storage v závislosti na vašem požadavku.
* Nástroj pro generalizaci VHD, pokud máte v plánu vytvořit z něj více instancí virtuálních počítačů. Například Sysprep pro Windows nebo Virt-Sysprep pro Ubuntu.
* Nástroj pro nahrání souboru VHD do účtu úložiště. Přečtěte si téma [přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md) nebo použití [Průzkumníka služby Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Tato příručka popisuje zkopírování VHD pomocí nástroje AzCopy.

> [!NOTE]
> Pokud zvolíte možnost synchronní kopírování s AzCopy a optimálním výkonem, zkopírujte virtuální pevný disk spuštěním jednoho z těchto nástrojů z virtuálního počítače Azure, který je ve stejné oblasti jako cílový účet úložiště. Pokud kopírujete virtuální pevný disk z virtuálního počítače Azure v jiné oblasti, může být výkon pomalejší.
>
> Pokud chcete kopírovat velké množství dat přes omezení šířky pásma, zvažte [použití služby Azure import/export k přenosu dat do BLOB Storage](../storage-import-export-service.md); Díky tomu můžete přenést data přenosem jednotek pevného disku do datacentra Azure. Službu Azure import/export můžete použít jenom ke kopírování dat do standardního účtu úložiště. Jakmile jsou data ve vašem účtu úložiště úrovně Standard, můžete k přenosu dat do účtu Premium Storage použít buď [kopírování rozhraní BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx) , nebo AzCopy.
>
> Všimněte si, že Microsoft Azure podporuje pouze pevné velikosti souborů VHD. Soubory VHDX nebo dynamické VHD se nepodporují. Pokud máte dynamický virtuální pevný disk, můžete ho převést na pevnou velikost pomocí rutiny [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Scénář 1: "Migruji jste stávající virtuální počítače Azure do Azure Premium Storage."
Pokud migrujete stávající virtuální počítače Azure, zastavte virtuální počítač, připravte virtuální pevné disky na požadovaný typ VHD a pak zkopírujte VHD pomocí AzCopy nebo PowerShellu.

Aby bylo možné migrovat čistý stav, musí být virtuální počítač kompletně vypnut. Až se migrace dokončí, dojde k výpadku.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Příprava VHD pro migraci
Pokud migrujete stávající virtuální počítače Azure do Premium Storage, může to mít tento virtuální pevný disk:

* Zobecněná bitová kopie operačního systému
* Jedinečný disk s operačním systémem
* Datový disk

Níže si projdeme tyto 3 scénáře přípravy VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Vytvoření více instancí virtuálních počítačů pomocí zobecněného virtuálního pevného disku s operačním systémem
Pokud nahráváte VHD, který se použije k vytvoření několika obecných instancí virtuálních počítačů Azure, musíte nejdřív provést generalizaci VHD pomocí nástroje Sysprep. To platí pro virtuální pevný disk, který je místní nebo v cloudu. Nástroj Sysprep odstraní z virtuálního pevného disku všechny informace specifické pro konkrétní počítač.

> [!IMPORTANT]
> Před generalizací vytvořte snímek nebo zálohujte svůj virtuální počítač. Spuštění nástroje Sysprep ukončí a uvolní instanci virtuálního počítače. Postupujte podle následujících kroků a proveďte nástroj Sysprep pro virtuální pevný disk s operačním systémem Windows. Všimněte si, že spuštění příkazu Sysprep bude vyžadovat, abyste virtuální počítač vypnuli. Další informace o nástroji Sysprep najdete v tématu [Přehled nástroje Sysprep](https://technet.microsoft.com/library/hh825209.aspx) nebo [technické Reference k nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otevřete okno příkazového řádku jako správce.
2. Spusťte nástroj Sysprep zadáním následujícího příkazu:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. V nástroji pro přípravu systému vyberte možnost spustit systém při spuštění prostředí (OOBE), zaškrtněte políčko generalizace, vyberte možnost **vypnout**a pak klikněte na tlačítko **OK**, jak je znázorněno na následujícím obrázku. Nástroj Sysprep provede generalizaci operačního systému a vypne systém.

    ![][1]

Pro virtuální počítač s Ubuntu použijte Virt-Sysprep, abyste mohli dosáhnout stejného. Další podrobnosti najdete v tématu [Virt-Sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) . Podívejte se také na některý z otevřených zdrojového [softwaru Linux serveru](https://www.cyberciti.biz/tips/server-provisioning-software.html) pro jiné operační systémy Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Vytvoření jedné instance virtuálního počítače pomocí jedinečného virtuálního pevného disku s operačním systémem
Pokud máte na VIRTUÁLNÍm počítači spuštěnou aplikaci, která vyžaduje data specifická pro konkrétní počítač, neprovádějte generalizaci VHD. Nezobecněný virtuální pevný disk se dá použít k vytvoření jedinečné instance virtuálního počítače Azure. Pokud máte například řadič domény na virtuálním pevném disku, spuštění nástroje Sysprep nebude mít žádný dopad jako řadič domény. Před generalizací VHD zkontrolujte spuštěné aplikace na vašem VIRTUÁLNÍm počítači a dopad spuštění programu Sysprep.

##### <a name="register-data-disk-vhd"></a>Registrovat virtuální pevný disk datového disku
Pokud máte datové disky v Azure, které se mají migrovat, musíte se ujistit, že jsou virtuální počítače, které používají tyto datové disky, vypnuté.

Pomocí kroků popsaných níže zkopírujte virtuální pevný disk do Azure Premium Storage a zaregistrujte ho jako zřízený datový disk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvoření cíle pro virtuální pevný disk
Vytvořte účet úložiště pro správu virtuálních pevných disků. Při plánování, kam se mají ukládat virtuální pevné disky, vezměte v úvahu následující body:

* Cílový účet Premium Storage.
* Umístění účtu úložiště musí být stejné jako Premium Storage podporující virtuální počítače Azure, které budete vytvářet v konečné fázi. Mohli byste zkopírovat do nového účtu úložiště nebo naplánovat použití stejného účtu úložiště podle vašich potřeb.
* Zkopírujte a uložte klíč účtu úložiště cílového účtu úložiště pro další fázi.

U datových disků si můžete vybrat, jestli chcete zachovat některé datové disky v účtu úložiště úrovně Standard (například disky s chladicím úložištěm), ale důrazně doporučujeme, abyste všechna data pro produkční úlohy převedli na využívání služby Premium Storage.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3. Kopírování VHD pomocí AzCopy nebo PowerShellu
Abyste mohli zpracovat jednu z těchto dvou možností, budete muset najít cestu ke kontejneru a klíč účtu úložiště. Cesta ke kontejneru a klíč účtu úložiště najdete na webu **Azure Portal** > **Storage**. Adresa URL kontejneru bude jako https:\//myaccount.blob.Core.Windows.NET/myContainer/.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Možnost 1: Kopírování VHD pomocí AzCopy (asynchronní kopírování)
Pomocí AzCopy můžete snadno nahrát virtuální pevný disk přes Internet. V závislosti na velikosti virtuálních pevných disků může to chvíli trvat. Při použití této možnosti Nezapomeňte ověřit omezení pro příchozí/odchozí přenos účtu úložiště. Podrobnosti najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md) .

1. Stáhněte a nainstalujte AzCopy odsud: [Nejnovější verze AzCopy](https://aka.ms/downloadazcopy)
2. Otevřete Azure PowerShell a přejdete do složky, kde je nainstalovaná AzCopy.
3. Pomocí následujícího příkazu zkopírujte soubor VHD ze zdroje do umístění cíl.

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Příklad:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Tady jsou popisy parametrů použitých v příkazu AzCopy:

   * **/Source:** _zdroj:&gt; &lt;_ Umístění složky nebo adresy URL kontejneru úložiště, která obsahuje virtuální pevný disk.
   * **/SourceKey:** klíč účtu zdroje:  _&lt;&gt;_ Klíč účtu úložiště zdrojového účtu úložiště
   * **/Dest:** _cíl:&gt; &lt;_ Adresa URL kontejneru úložiště, do kterého se má zkopírovat VHD
   * **/DestKey:** _cílový účet-klíč&gt;: &lt;_ Klíč účtu úložiště cílového účtu úložiště.
   * **/Pattern:** _názevsouboru&gt;: &lt;_ Zadejte název souboru VHD, který se má zkopírovat.

Podrobnosti o používání nástroje AzCopy Tool najdete v tématu [přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Možnost 2: Kopírování VHD pomocí PowerShellu (synchronizovaná kopie)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Soubor VHD můžete zkopírovat také pomocí rutiny PowerShellu Start-AzStorageBlobCopy. K zkopírování VHD použijte následující příkaz na Azure PowerShell. Hodnoty v < > nahraďte odpovídajícími hodnotami ze zdrojového a cílového účtu úložiště. Pokud chcete tento příkaz použít, musíte mít ve svém cílovém účtu úložiště kontejner s názvem VHD. Pokud kontejner neexistuje, vytvořte ho před spuštěním příkazu.

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

### <a name="scenario2"></a>Scénář 2: "Migruji jste virtuální počítače z jiných platforem do Azure Premium Storage."
Pokud migrujete VHD z jiného než Azure cloudového úložiště do Azure, musíte nejdřív exportovat virtuální pevný disk do místního adresáře. Použijte úplnou cestu ke zdroji místního adresáře, kde je virtuální pevný disk uložený, a pak pomocí AzCopy ho nahrajte do Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Export virtuálního pevného disku do místního adresáře
##### <a name="copy-a-vhd-from-aws"></a>Kopírování VHD z AWS
1. Pokud používáte AWS, exportujte instanci EC2 do virtuálního pevného disku v kontejneru Amazon S3. Postupujte podle kroků popsaných v dokumentaci k Amazonu pro export instance Amazon EC2, abyste nainstalovali nástroj Amazon EC2 rozhraní příkazového řádku (CLI) a spustili příkaz Create-instance-export-Task, který vyexportuje instanci EC2 do souboru VHD. Při spuštění příkazu **Create-instance-export-Task** nezapomeňte pro proměnnou formátu&#95;image&#95;disku použít **VHD** . Exportovaný soubor VHD se uloží do kontejneru Amazon S3, který jste určili během tohoto procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Stáhněte soubor VHD z intervalu S3. Vyberte soubor VHD a pak **Akce** > **Stáhnout**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopírování VHD z jiného cloudu mimo Azure
Pokud migrujete VHD z jiného než Azure cloudového úložiště do Azure, musíte nejdřív exportovat virtuální pevný disk do místního adresáře. Zkopírujte úplnou cestu ke zdroji místního adresáře, kde je virtuální pevný disk uložený.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopírování VHD z místního prostředí
Pokud migrujete VHD z místního prostředí, budete potřebovat úplnou cestu ke zdroji, kde je uložený VHD. Zdrojovou cestou může být umístění serveru nebo sdílená složka.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Vytvoření cíle pro virtuální pevný disk
Vytvořte účet úložiště pro správu virtuálních pevných disků. Při plánování, kam se mají ukládat virtuální pevné disky, vezměte v úvahu následující body:

* V závislosti na požadavku vaší aplikace může být cílový účet úložiště Standard nebo Premium Storage.
* Oblast účtu úložiště musí být stejná jako Premium Storage podporující virtuální počítače Azure, které budete vytvářet v konečné fázi. Mohli byste zkopírovat do nového účtu úložiště nebo naplánovat použití stejného účtu úložiště podle vašich potřeb.
* Zkopírujte a uložte klíč účtu úložiště cílového účtu úložiště pro další fázi.

Důrazně doporučujeme, abyste všechna data pro produkční úlohy přesouvaný na využívání služby Premium Storage.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Nahrání virtuálního pevného disku do Azure Storage
Teď, když máte virtuální pevný disk v místním adresáři, můžete k nahrání souboru. VHD do Azure Storage použít AzCopy nebo AzurePowerShell. Obě možnosti jsou k dispozici zde:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Možnost 1: Použití Azure PowerShell Add-AzureVhd pro nahrání souboru. VHD

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Příkladem \<> identifikátoru URI **_může https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd_** být "". Příklad \<: FileInfo > může být **_"C:\path\to\upload.VHD"_** .

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Možnost 2: Nahrání souboru. VHD pomocí AzCopy
Pomocí AzCopy můžete snadno nahrát virtuální pevný disk přes Internet. V závislosti na velikosti virtuálních pevných disků může to chvíli trvat. Při použití této možnosti Nezapomeňte ověřit omezení pro příchozí/odchozí přenos účtu úložiště. Podrobnosti najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md) .

1. Stáhněte a nainstalujte AzCopy odsud: [Nejnovější verze AzCopy](https://aka.ms/downloadazcopy)
2. Otevřete Azure PowerShell a přejdete do složky, kde je nainstalovaná AzCopy.
3. Pomocí následujícího příkazu zkopírujte soubor VHD ze zdroje do umístění cíl.

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Příklad:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Tady jsou popisy parametrů použitých v příkazu AzCopy:

   * **/Source:** _zdroj:&gt; &lt;_ Umístění složky nebo adresy URL kontejneru úložiště, která obsahuje virtuální pevný disk.
   * **/SourceKey:** klíč účtu zdroje:  _&lt;&gt;_ Klíč účtu úložiště zdrojového účtu úložiště
   * **/Dest:** _cíl:&gt; &lt;_ Adresa URL kontejneru úložiště, do kterého se má zkopírovat VHD
   * **/DestKey:** _cílový účet-klíč&gt;: &lt;_ Klíč účtu úložiště cílového účtu úložiště.
   * **/BlobType: stránka:** Určuje, že cílem je objekt blob stránky.
   * **/Pattern:** _názevsouboru&gt;: &lt;_ Zadejte název souboru VHD, který se má zkopírovat.

Podrobnosti o používání nástroje AzCopy Tool najdete v tématu [přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti pro nahrání VHD
Virtuální pevný disk můžete také nahrát do svého účtu úložiště pomocí některého z následujících způsobů:

* [Azure Storage kopírovat rozhraní BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Průzkumník služby Azure Storage nahrávání objektů BLOB](https://azurestorageexplorer.codeplex.com/)
* [Referenční informace o REST API služby Import/Export úložiště](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Službu import/export doporučujeme používat, pokud je odhadovaný čas odeslání delší než 7 dní. Pomocí [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) můžete odhadnout čas od velikosti dat a jednotky přenosu.
>
> Import/export se dá použít ke zkopírování do standardního účtu úložiště. Pomocí nástroje, jako je AzCopy, budete muset zkopírovat ze standardního úložiště na účet Premium Storage.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Vytvoření virtuálních počítačů Azure pomocí Premium Storage
Po nahrání nebo zkopírování virtuálního pevného disku do požadovaného účtu úložiště postupujte podle pokynů v této části a zaregistrujte VHD jako bitovou kopii operačního systému nebo disk s operačním systémem v závislosti na vašem scénáři a pak z něj vytvořte instanci virtuálního počítače. Virtuální pevný disk datového disku můžete připojit k virtuálnímu počítači po jeho vytvoření.
Vzorový skript migrace je uvedený na konci této části. Tento jednoduchý skript neodpovídá všem scénářům. Je možné, že budete muset skript aktualizovat, aby odpovídal vašemu konkrétnímu scénáři. Pokud chcete zjistit, jestli tento skript platí pro váš scénář, přečtěte si část [ukázkový skript migrace](#a-sample-migration-script).

### <a name="checklist"></a>Kontrolní seznam
1. Počkejte, než se dokončí kopírování všech disků VHD.
2. Ujistěte se, že Premium Storage k dispozici v oblasti, do které migrujete.
3. Určete novou řadu virtuálních počítačů, kterou budete používat. Měl by to být Premium Storage schopný a velikost by měla být závislá na dostupnosti v oblasti a podle vašich potřeb.
4. Určete přesnou velikost virtuálního počítače, kterou budete používat. Velikost virtuálního počítače musí být dostatečně velká, aby bylo možné podporovat počet datových disků, které máte. Například Pokud máte 4 datové disky, musí mít virtuální počítač 2 nebo více jader. Vezměte v úvahu také požadavky na výkon napájení, paměti a šířky pásma sítě.
5. Vytvořte účet Premium Storage v cílové oblasti. Jedná se o účet, který budete používat pro nový virtuální počítač.
6. Podrobnější informace o aktuálním virtuálním počítači, včetně seznamu disků a odpovídajících objektů BLOB VHD

Připraví aplikaci na výpadky. Chcete-li provést čistou migraci, je nutné zastavit veškeré zpracování v aktuálním systému. Teprve potom ho můžete získat do konzistentního stavu, který můžete migrovat na novou platformu. Doba výpadku bude záviset na množství dat na discích, které se mají migrovat.

> [!NOTE]
> Pokud vytváříte virtuální počítač Azure Resource Manager ze specializovaného disku VHD, přečtěte si prosím [tuto šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) pro nasazení Správce prostředků virtuálního počítače pomocí existujícího disku.
>
>

### <a name="register-your-vhd"></a>Registrace VHD
Pokud chcete vytvořit virtuální počítač z virtuálního pevného disku s operačním systémem nebo připojit datový disk k novému virtuálnímu počítači, musíte je nejdřív zaregistrovat. Postupujte podle následujících kroků v závislosti na scénáři vašeho virtuálního pevného disku.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Zobecněný virtuální pevný disk s operačním systémem pro vytvoření více instancí virtuálních počítačů Azure
Po odeslání obecného virtuálního pevného disku VHD do účtu úložiště ho zaregistrujete jako **image virtuálního počítače Azure** , abyste z něho mohli vytvořit jednu nebo víc instancí virtuálních počítačů. Použijte následující rutiny PowerShellu k registraci VHD jako image operačního systému virtuálního počítače Azure. Zadejte úplnou adresu URL kontejneru, do které se zkopíroval virtuální pevný disk (VHD).

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Zkopírujte a uložte název této nové image virtuálního počítače Azure. V předchozím příkladu je to *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečný virtuální pevný disk operačního systému pro vytvoření jedné instance virtuálního počítače Azure
Po nahrání jedinečného virtuálního pevného disku s operačním systémem do účtu úložiště ho zaregistrujete jako **disk s operačním systémem Azure** , abyste z něj mohli vytvořit instanci virtuálního počítače. Pomocí těchto rutin PowerShellu zaregistrujete VHD jako disk s operačním systémem Azure. Zadejte úplnou adresu URL kontejneru, do které se zkopíroval virtuální pevný disk (VHD).

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Zkopírujte a uložte název tohoto nového disku s operačním systémem Azure. V předchozím příkladu je to *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Virtuální pevný disk datového disku, který se má připojit k novým instancím virtuálních počítačů Azure
Po nahrání virtuálního pevného disku s datovým diskem do účtu úložiště ho zaregistrujte jako datový disk Azure, aby se mohl připojit k nové řadě DS, DSv2 sérii nebo k instanci virtuálního počítače Azure řady GS.

Pomocí těchto rutin PowerShellu zaregistrujete VHD jako datový disk Azure. Zadejte úplnou adresu URL kontejneru, do které se zkopíroval virtuální pevný disk (VHD).

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Zkopírujte a uložte název tohoto nového datového disku Azure. V předchozím příkladu je to datadisk.

### <a name="create-a-premium-storage-capable-vm"></a>Vytvoření virtuálního počítače podporujícího Premium Storage
Po registraci image operačního systému nebo disku s operačním systémem vytvořte nový virtuální počítač řady DS-Series, DSv2-Series nebo GS-Series. Budete používat bitovou kopii operačního systému nebo název disku operačního systému, který jste zaregistrovali. Z Premium Storage úrovně vyberte typ virtuálního počítače. V následujícím příkladu používáme velikost virtuálního počítače *Standard_DS2* .

> [!NOTE]
> Aktualizujte velikost disku, abyste se ujistili, že odpovídá vašim požadavkům na kapacitu a výkon a dostupným velikostem disků Azure.
>
>

Pokud chcete vytvořit nový virtuální počítač, postupujte podle pokynů v části Krok za krokem prostředí PowerShell. Nejdřív nastavte společné parametry:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Nejdřív vytvořte cloudovou službu, ve které budete hostovat svoje nové virtuální počítače.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

V závislosti na vašem scénáři vytvořte instanci virtuálního počítače Azure z image operačního systému nebo disku s operačním systémem, který jste zaregistrovali.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Zobecněný virtuální pevný disk s operačním systémem pro vytvoření více instancí virtuálních počítačů Azure
Vytvořte jednu nebo více nových instancí virtuálních počítačů Azure řady DS pomocí **image operačního systému Azure** , kterou jste zaregistrovali. Pokud vytváříte nový virtuální počítač, zadejte tento název bitové kopie operačního systému v konfiguraci virtuálního počítače, jak je znázorněno níže.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Jedinečný virtuální pevný disk operačního systému pro vytvoření jedné instance virtuálního počítače Azure
Pomocí **disku s operačním systémem Azure** , který jste zaregistrovali, vytvořte novou instanci virtuálního počítače Azure řady DS. Zadejte tento název disku operačního systému v konfiguraci virtuálního počítače při vytváření nového virtuálního počítače, jak je znázorněno níže.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Zadejte další informace o virtuálním počítači Azure, jako je cloudová služba, oblast, účet úložiště, Skupina dostupnosti a zásady ukládání do mezipaměti. Všimněte si, že instance virtuálního počítače musí být umístěná společně s přidruženým operačním systémem nebo datovými disky, takže vybraná cloudová služba, oblast a účet úložiště musí být ve stejném umístění jako příslušné virtuální pevné disky těchto disků.

### <a name="attach-data-disk"></a>Připojení datového disku
Pokud jste zaregistrovali virtuální pevné disky datových disků, připojte je k novému virtuálnímu počítači Azure, který podporuje Premium Storage.

Pomocí následující rutiny prostředí PowerShell připojte datový disk k novému virtuálnímu počítači a určete zásady ukládání do mezipaměti. V následujícím příkladu je zásada ukládání do mezipaměti nastavená na *jen pro čtení*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Pro podporu aplikace, na kterou se tato příručka nevztahuje, můžou být potřeba další kroky.
>
>

### <a name="checking-and-plan-backup"></a>Kontrola a plánování zálohování
Po zprovoznění nového virtuálního počítače a jeho použití se stejným přihlašovacím ID a heslem je původní virtuální počítač a ověřte, že vše funguje podle očekávání. Všechna nastavení, včetně prokládaných svazků, by byla k dispozici v novém virtuálním počítači.

Posledním krokem je plánování plánu zálohování a údržby pro nový virtuální počítač na základě potřeb aplikace.

### <a name="a-sample-migration-script"></a>Vzorový migrační skript
Pokud máte víc virtuálních počítačů, které se mají migrovat, budete mít k dispozici automatizaci prostřednictvím skriptů PowerShellu. Následuje ukázkový skript, který automatizuje migraci virtuálního počítače. Všimněte si, že pod skriptem je jenom příklad, a na aktuálních discích virtuálních počítačů se udělalo několik předpokladů. Je možné, že budete muset skript aktualizovat, aby odpovídal vašemu konkrétnímu scénáři.

Předpoklady:

* Vytváříte klasické virtuální počítače Azure.
* Zdrojové disky s operačním systémem a zdrojové datové disky jsou ve stejném účtu úložiště a stejném kontejneru. Pokud disky s operačním systémem a datové disky nejsou na stejném místě, můžete k kopírování VHD přes účty úložiště a kontejnery použít AzCopy nebo Azure PowerShell. Přečtěte si předchozí krok: [Zkopírujte virtuální pevný disk pomocí AzCopy nebo PowerShellu](#copy-vhd-with-azcopy-or-powershell). Úprava tohoto skriptu tak, aby splňovala váš scénář, je další volbou, ale doporučujeme používat AzCopy nebo PowerShell, protože je jednodušší a rychlejší.

Skript automatizace je uvedený níže. Nahraďte text vašimi informacemi a aktualizujte skript tak, aby odpovídal vašemu konkrétnímu scénáři.

> [!NOTE]
> Použití existujícího skriptu nezachovává síťovou konfiguraci zdrojového virtuálního počítače. Na migrovaných virtuálních počítačích budete muset znovu konfigurovat nastavení sítě.
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

#### <a name="optimization"></a>Vybrané
Vaše aktuální konfigurace virtuálních počítačů může být upravena speciálně pro práci na standardních discích. Například ke zvýšení výkonu pomocí řady disků v prokládaném svazku. Například namísto použití 4 disků samostatně na Premium Storage může být možné optimalizovat náklady s použitím jediného disku. Optimalizace, jako je to potřeba, se musí v jednotlivých případech zpracovávat a vyžadují vlastní kroky po migraci. Také Upozorňujeme, že tento proces nemusí dobře fungovat pro databáze a aplikace, které závisí na rozložení disku definovaném v instalačním programu.

##### <a name="preparation"></a>Příprava
1. Proveďte jednoduchou migraci, jak je popsáno v předchozí části. Optimalizace budou provedeny na novém virtuálním počítači po dokončení migrace.
2. Definujte nové velikosti disků potřebné pro optimalizovanou konfiguraci.
3. Určete mapování aktuálních disků nebo svazků na nové specifikace disků.

##### <a name="execution-steps"></a>Kroky provedení
1. Vytvořte nové disky se správnými velikostmi na Premium Storagem virtuálním počítači.
2. Přihlaste se k virtuálnímu počítači a zkopírujte data z aktuálního svazku na nový disk, který se mapuje na tento svazek. Udělejte to pro všechny aktuální svazky, které potřebují namapovat na nový disk.
3. Pak změňte nastavení aplikace tak, aby se přepnulo na nové disky, a odpojte staré svazky.

Chcete-li ladit aplikaci pro lepší výkon disku, přečtěte si část věnované optimalizaci výkonu aplikace v našem článku [Návrh pro zajištění vysokého výkonu](../../virtual-machines/windows/premium-storage-performance.md) .

### <a name="application-migrations"></a>Migrace aplikací
Databáze a další komplexní aplikace mohou vyžadovat zvláštní postup definovaný poskytovatelem aplikace pro migraci. Přečtěte si příslušnou dokumentaci k aplikaci. Například databáze se obvykle dají migrovat pomocí zálohování a obnovení.

## <a name="next-steps"></a>Další kroky
Konkrétní scénáře migrace virtuálních počítačů najdete v následujících zdrojích informací:

* [Migrace Azure Virtual Machines mezi účty úložiště](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Vytvořte a nahrajte virtuální pevný disk s Windows serverem do Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření a nahrání virtuálního pevného disku se systémem Linux do Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace Virtual Machines z Amazon AWS do Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Další informace o Azure Storage a službě Azure Virtual Machines najdete v následujících zdrojích informací:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Vyberte typ disku pro virtuální počítače s IaaS.](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
