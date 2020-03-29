---
title: Microsoft Azure Data Box Disk – nejčastější dotazy | Microsoft Docs
description: Obsahuje často kladené otázky a odpovědi pro Azure Data Box Disk, cloudové řešení, které vám umožní přenášet velké množství dat do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 7ba6ea8606fc354527ff4114bc45a0904941ba93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918933"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Disk datové schránky Azure: Nejčastější dotazy

Cloudové řešení Microsoft Azure Data Box Disk umožňuje odesílat do Azure rychle, levně a bezpečně terabajty dat. Tyto nejčastější dotazy obsahují otázky a odpovědi, které se týkají používání disků Data Box Disk na webu Azure Portal. 

Otázky a odpovědi jsou uspořádané do těchto kategorií:

- O službě
- Konfigurace a připojení 
- Sledování stavu
- Migrace dat 
- Ověření a nahrání dat 


## <a name="about-the-service"></a>O službě

### <a name="q-what-is-azure-data-box-service"></a>Otázka: Co je služba Azure Data Box? 
A.  Služba Azure Data Box je určená pro příjem dat offline. Tato služba spravuje celou řadu produktů, které jsou přizpůsobené pro přenos dat pro různé kapacity úložiště. 

### <a name="q-what-are-azure-data-box-disks"></a>Otázka: Co jsou disky Azure Data Box Disk?
A. Disky Azure Data Box Disk umožňují rychlý, levný a bezpečný přenos terabajtů dat do Azure a z Azure. Microsoft vám dodá 1 až 5 disků s maximální kapacitou úložiště 35 TB. Tyto disky můžete pomocí služby Data Box na webu Azure Portal snadno nakonfigurovat, připojit a odemknout.  

Disky jsou šifrované pomocí nástroje Microsoft BitLocker Drive Encryption a ke správě šifrovacích klíčů se využívá Azure Portal. Potom zkopírujete příslušná data ze serverů zákazníka. V datovém centru provede Microsoft migraci vašich dat z jednotky do cloudu pomocí rychlého nahrávacího propojení privátní sítě a nahraje je do Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>Otázka: Kdy je vhodné použít disky Data Box Disk?
A. Pokud máte 40 TB dat (nebo méně), které chcete přenést do Azure, je pro vás použití disků Data Box Disk výhodné.

### <a name="q-what-is-the-price-of-data-box-disks"></a>Otázka: Jaká je cena disků Data Box Disk?
A. Informace o ceně disků datových schronů naleznete na [stránce Ceny](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>Otázka: Jak lze disky Data Box Disk získat? 
A.  Pokud chcete získat disky Azure Data Box, přihlaste se na portál Azure portal a vytvořte pořadí datových schráží pro disky. Zadejte svoje kontaktní údaje a podrobnosti o oznámení. Po provedení objednávky vám budou disky dodány podle dostupnosti do 10 dnů.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Otázka: Jaký je maximální objem dat, který se dá přenést pomocí disků Data Box Disk v jedné instanci?
A. Při 5 discích, kdy každý má velikost 8 TB (7 TB využitelné kapacity), je maximální využitelná kapacita 35 TB. V jedné instanci je tedy možné přenést 35 TB dat. Pro přenos většího objemu dat je nutné objednat další disky.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Otázka: Jak zjistím, jestli jsou disky Data Box Disk dostupné v mojí oblasti? 
A.  Chcete-li zjistit, kde jsou disky datových schronů aktuálně k dispozici, přejděte na informace o [dostupnosti oblasti](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Otázka: Ve kterých oblastech lze data ukládat pomocí disků Data Box Disk?
A. Disk datové schránky je podporován pro všechny oblasti v USA, Kanadě, Austrálii, západní Evropě a severní Evropě, Koreji a Japonsku. Podporované jsou jenom oblasti veřejného cloudu Azure. Azure Government ani jiné suverénní cloudy nejsou podporované.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>Otázka: Překročí můj disk datové schránky během přepravy hranice země?
A. Disk datové schránky je dodáván ze stejné země jako cíl a nepřekročí žádné mezinárodní hranice. Jedinou výjimkou jsou objednávky v Evropské unii (EU), kde mohou být disky odesílány do a z kterékoli země EU.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Otázka: Na koho se mám obrátit, když narazím na nějaké problémy s disky Data Box Disk?
A. Pokud narazíte na nějaké problémy s disky datových schronů, [obraťte se na podporu společnosti Microsoft](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Konfigurace a připojení
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Otázka: Můžu v objednávce určit počet disků Data Box Disk?
A.  Ne. Získáte disky o velikosti 8 TB (maximálně 5 disků), podle objemu vašich dat a dostupnosti disků.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Otázka: Jak disky Data Box Disk odemknu? 
A.  Na webu Azure Portal přejděte k vaší objednávce disků Data Box Disk a přejděte na **podrobnosti o zařízení**. Zkopírujte klíč. Stáhněte a rozbalte odemykací nástroj Data Box Disku z webu Azure Portal do operačního systému. Spusťte tento nástroj v počítači s daty, která chcete zkopírovat na disky. Zadejte klíč k odemknutí disků. Stejný klíč odemkne všechny disky. 

Podrobný postup najdete v článku o [odemknutí disků v klientovi pro Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) nebo o [odemknutí disků v klientovi pro Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Otázka: Můžu k připojení a zkopírování dat na disky Data Box Disk použít hostitelský počítač Linux?
A.  Ano. K připojení a zkopírování dat na Data Box Disky je možné použít klienty pro Linux i Windows. Další informace získáte, když přejdete na seznam [podporovaných operačních systémů](data-box-disk-system-requirements.md) hostitelského počítače.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Otázka: Moje disky se odeslaly, ale já teď chci tuto objednávku zrušit. Proč není dostupné tlačítko pro zrušení?
A.  Objednávku lze po vytvoření zrušit jenom do doby její expedice. Jakmile se disky odešlou, není už možné danou objednávku zrušit. Disky však můžete vrátit za poplatek. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Otázka: Můžu pro přenos dat připojit k hostitelskému počítači více disků Data Box Disk současně?
A. Ano. Pro přenos dat lze k jednomu hostitelskému počítači připojit více disků Data Box Disk a několik úloh kopírování může běžet paralelně.

## <a name="track-status"></a>Sledování stavu

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Otázka: Jak můžu sledovat disky od vytvoření objednávky až po vrácení disků? 
A.  Stav objednávky disků Data Box Disk můžete sledovat na webu Azure Portal. Při vytváření objednávky se zobrazuje také výzva k zadání e-mailové adresy pro oznámení. Pokud jste tuto adresu zadali, budete prostřednictvím e-mailu dostávat oznámení o všech změnách stavu dané objednávky. Další informace o [konfiguraci e-mailových adres pro oznámení](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>Otázka: Jak můžu disky vrátit? 
A.  Microsoft přikládá k balíčku s disky Data Box Disk expediční štítek. Připevněte tento štítek k odesílanému balíčku a zavezte zapečetěný balíček na pobočku dopravce. Pokud se tento štítek poškodil nebo ztratil, přejděte na **Přehled > Stáhnout expediční štítek** a stáhněte si nový zpětný expediční štítek.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Mohu si svou objednávku data boxu objednat sám? Mohu disky vrátit prostřednictvím zvoleného operátora?
A. Ano. Společnost Microsoft také nabízí samoobslužnou dopravu pouze v oblasti US Gov. Při zadávání objednávky datové schránky disku můžete zvolit možnost samořízeného odesílání. Chcete-li vyzvednout objednávku disku datové schránky, postupujte takto:
    
1. Po zařízení objednávky je objednávka zpracována a disky připraveny. E-mailem budete upozorněni, že vaše objednávka je připravena k vyzvednutí. 
2. Jakmile je objednávka připravená k vyzvednutí, přejděte na svůj řád na webu Azure portal a přejděte do okna **Přehled.** 
3. Na webu Azure Portal se zobrazí oznámení s kódem. Pošlete e-mail týmu [Azure Data Box Operations](mailto:adbops@microsoft.com) a poskytněte mu kód. Tým poskytne místo a naplánuje datum a čas vyzvednutí. Musíte zavolat týmu do 5 pracovních dnů poté, co obdržíte e-mailové oznámení.

Po dokončení kopírování a ověřování dat proveďte následující kroky k vrácení disku:

1. Po dokončení ověření dat odpojte disky. Odeberte propojovací kabely.
2. Zabalte všechny disky a propojovací kabely do bublinkové fólie a vložte je do přepravního boxu. Pokud chybí příslušenství, můžou se účtovat poplatky.

    - Znovu použijte balení z prvotní dodávky. Doporučujeme zabalit disky pomocí odolné bublinkové fólie.
    - Ujistěte se, že vše dobře sedí, aby se zamezilo jakýmkoli posunům v krabici.
3. Přejděte na **okno Přehled** pro vaši objednávku na webu Azure Portal. Měli byste vidět oznámení s kódem.
4. Použijte tento kód a pošlete e-mail [týmu Azure Data Box Operations](mailto:adbops@microsoft.com) a zadejte jim kód. Poskytnou vám informace o tom, kde a kdy je disky odsouvat.


## <a name="migrate-data"></a>Migrace dat

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Otázka: Jaká je maximální velikost dat, která se dá použít s disky Data Box Disk?  
A.  Řešení s disky Data Box Disk může mít až 5 disků s maximální využitelnou kapacitou 35 TB. Samotné disky mají velikost 8 TB (využitelných je 7 TB).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Otázka: Jaké maximální velikosti objektu blob bloku a objektu blob stránky disky Data Box Disk podporují? 
A.  Maximální velikosti se řídí omezeními služby Azure Storage. Maximální velikost objektu blob bloku je přibližně 4,768 TiB a maximální velikost objektu blob stránky je 8 TiB. Další informace najdete v tématu [Škálovatelnost a cíle výkonu pro úložiště objektů Blob](../storage/blobs/scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Otázka: Jaká je u disků Data Box Disk rychlost přenosu dat?
A. Při testování s disky připojenými přes USB 3.0 byl výkon disku až 430 MB/s. Skutečné hodnoty se liší v závislosti na velikosti použitých souborů. U menších souborů může být výkon nižší.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Otázka: Jak zajistím, aby byla data při přenosu zabezpečená? 
A.  Disky Data Box Disk se šifrují pomocí 128bitového šifrování AES nástroje BitLocker a klíč je dostupný jenom na webu Azure Portal. Pokud chcete tento klíč získat, přihlaste se na web Azure Portal pomocí přihlašovacích údajů k účtu. Po spuštění nástroje pro odemykání disků Data Box Disk zadejte tento klíč.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Otázka: Jak můžu kopírovat data na disky Data Box Disk? 
A.  Ke kopírování dat na disky použijte nástroj pro kopírování SMB, například Robocopy, Diskboss nebo třeba i přetažení v Průzkumníkovi souborů Windows.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Otázka: Máte nějaké tipy pro zrychlení kopírování dat?
A.  Pokud chcete zrychlit proces kopírování:

- Použijte pro kopírování dat více streamů. Například v Robocopy použijte možnost více vláken. Přesnější informace o používaných příkazech získáte v [kurzu, který se týká kopírování dat na disk Azure Data Box Disk a ověření](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Použijte více relací.
- Místo kopírování přes sdílenou síťovou složku (při kterém vás můžou omezovat rychlosti sítí) zajistěte, aby se data nacházela přímo na počítači, ke kterému jsou příslušné disky připojené.
- Zkontrolujte, že během procesu kopírování používáte USB 3.0 nebo novější. K identifikaci řadičů USB a zařízení USB připojených k počítači si stáhněte a používejte [nástroj USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview).
- Proveďte srovnávací testy výkonu počítače, který se používá ke kopírování dat. Pro srovnávací testy výkonu hardwaru serveru si stáhněte a používejte [nástroj Bluestop FIO](https://ci.appveyor.com/project/axboe/fio). Vyberte nejnovější sestavení x86 nebo x64, vyberte kartu **Artefakty** a stáhněte si MSI.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Otázka: Jak se dá kopírování zrychlit, když jsou zdrojová data tvořená malými soubory (o velikosti v kB nebo několika málo MB)?
A.  Pokud chcete zrychlit proces kopírování:

- Vytvořte místní disk VHDx v rychlém úložišti nebo prázdný virtuální pevný disk na disku HDD/SSD (pomalejší).
- Připojte ho k virtuálnímu počítači.
- Zkopírujte soubory na disk tohoto virtuálního počítače.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Otázka: Můžu s disky Data Box Disk používat více účtů úložiště?
A.  Ne. Pro použití s disky Data Box Disk se momentálně podporuje jenom jeden účet úložiště, obecný nebo klasický. Podporují se horké i studené objekty blob. V současné době jsou podporované jenom účty úložiště v USA, západní Evropě a severní Evropě ve veřejném cloudu Azure.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>Otázka: Jaká je sada nástrojů dostupná pro moje data s disky datových schronů?
A. Sada nástrojů, která je k dispozici s diskem datové schránky, obsahuje tři nástroje:
 - **Nástroj pro odemknutí disku datové schránky**: Pomocí tohoto nástroje odemkněte šifrované disky dodávané od společnosti Microsoft. Při odemknutí disků pomocí nástroje je potřeba zadat klíč, který je k dispozici v pořadí disků datové schránky na webu Azure Portal. 
 - **Nástroj pro ověřování disku datovéschránky**: Tento nástroj slouží k ověření velikosti, formátu a názvů objektů blob podle konvencí pojmenování Azure. Generuje také kontrolní součty pro zkopírovaná data, která se pak používají k ověření dat nahraných do Azure.
 - **Nástroj rozdělení disku datové schránky**: Tento nástroj použijte, pokud používáte více disků a máte velkou datovou sadu, kterou je třeba rozdělit a zkopírovat na všechny disky. Tento nástroj je v současné době k dispozici pro systém Windows. Tento nástroj není podporován spravovanými disky. Tento nástroj také ověřuje, jak to kopíruje data, proto můžete přeskočit krok ověření při použití tohoto nástroje.

Sada nástrojů je k dispozici jak pro Windows, tak pro Linux. Sadu nástrojů si můžete stáhnout zde:
- [Stáhnout sadu nástrojů Data Box Disk pro Windows](https://aka.ms/databoxdisktoolswin) 
- [Stáhnout sadu nástrojů Data Box Disk pro Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>Otázka: Můžu použít disk datové schránky k přenosu dat do souborů Azure a pak je použít pomocí Azure File Sync? 
A. Soubory Azure jsou podporované pomocí disku datové schránky, ale nebudou dobře fungovat s Azure File Sync. Metadata se nezachovají, pokud se data souboru používají se synchronizací souborů Azure.


## <a name="verify-and-upload"></a>Ověření a nahrání

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Otázka: Za jak dlouho od vrácení disků budu mít přístup ke svým datům v Azure? 
A.  Jakmile se daná objednávka kopírování dat zobrazí jako dokončená, měli byste mít přístup k vašim datům.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Otázka: Kde v Azure se moje data po nahrání nachází?
A.  Při kopírování dat ve složkách *BlockBlob* a *PageBlob* na vašem disku se pro každou podsložku ve složkách *BlockBlob* a *PageBlob* vytvoří kontejner v účtu Azure Storage. Pokud jste zkopírovali soubory ve složkách *BlockBlob* a *PageBlob* přímo, pak se v účtu Azure Storage nachází ve výchozím kontejneru *$root*. Když zkopírujete data do složky ve složce *AzureFile,* vytvoří se sdílení souborů.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Otázka: Zjistil(a) jsem, že jsem nedodržel(a) požadavky Azure na názvy kontejnerů. Znamená to, že nahrání mých dat do Azure se nezdaří?
A. Pokud názvy kontejnerů obsahují velká písmena, automaticky se převedou na malá písmena. Pokud názvy nedodržují jiné požadavky (speciální znaky, ostatní jiné jazyky atd.), nahrání se nezdaří. Další informace najdete v článku o [zásadách vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Otázka: Jak můžu ověřit data, která jsem zkopíroval(a) na více disků Data Box Disk?
A.  Po dokončení kopírování dat můžete spuštěním příkazu `DataBoxDiskValidation.cmd`, který se nachází ve složce *DataBoxDiskImport*, vygenerovat kontrolní součty pro ověření. Pokud máte více disků, je nutné pro každý disk otevřít příkazové okno a v něm tento příkaz spustit. Upozorňujeme, že tato operace může v závislosti na velikosti vašich dat trvat delší dobu (řádově hodiny).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Otázka: Co se s mými daty stane po vrácení disků?
A.  Po dokončení kopírování dat do Azure se data z disků bezpečně vymažou v souladu se standardem NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>Otázka: Jak jsou data během přenosu chráněná? 
A.  Disky Data Box Disk používají šifrování AES-128 nástrojem Microsoft BitLocker. Pro odemknutí všech disků a přístup k datům se vyžaduje jediný klíč.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Otázka: Když na disky Data Box Disk přidám další data, je nutné znovu spustit ověření kontrolním součtem?
A. Ano. Pokud jste se rozhodli ověřovat data (což doporučujeme), bude nutné po přidání dalších dat na tyto disky znovu spustit ověření.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Otázka: Využil(a) jsem k přenosu dat všechny svoje disky a potřebuji objednat další disky. Existuje nějaký rychlý způsob, jak tuto objednávku vytvořit?
A. Můžete svoji předchozí objednávku naklonovat. Naklonováním se vytvoří stejná objednávka, jako byla ta předchozí. Podrobnosti této objednávky však můžete upravit. Nebudete tedy muset znovu zadávat adresu, kontaktní údaje a podrobnosti o oznámení.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>Otázka: Zkopíroval jsem data do složky ManagedDisk. Nejsou známy žádné spravované disky se skupinou prostředků určenou pro spravované disky. Byla moje data nahrána do Azure a jak je můžu najít?
A. Ano. Vaše data byla odeslána do Azure, ale pokud nevidíte žádné spravované disky se zadanými skupinami prostředků, je to pravděpodobně proto, že data nebyla platná. Pokud objekty BLOB stránky, objekty BLOB bloku, soubory Azure a spravované disky nejsou platné, přejdou do následujících složek:
 - Objekty BLOB stránky by přejít do kontejneru blob bloku počínaje *databoxdisk-invalid-pb-*.
 - Soubory Azure by přejít na kontejner blob bloku počínaje *databoxdisk-invalid-af-*.
 - Spravované disky by přešely do kontejneru objektů blob bloku počínaje *databoxdisk-invalid-md-*.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [požadavky na systém Datové schránky Disk](data-box-disk-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Disk](data-box-disk-limits.md).
- Rychlé nasazení řešení [Azure Data Box Disk](data-box-disk-quickstart-portal.md) na webu Azure Portal
