---
title: Microsoft Azure Data Box Disk – nejčastější dotazy | Microsoft Docs
description: Obsahuje nejčastější otázky a odpovědi týkající se Azure Data Box Disk, cloudového řešení, které umožňuje přenos velkých objemů dat do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 4320e56b04761c6adcae1db259eeecf332fbd781
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158784"
---
# <a name="what-is-azure-data-box-disk"></a>Co je Azure Data Box Disk?

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
A. Další informace o cenách disků Data Box [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>Otázka: Jak lze disky Data Box Disk získat? 
A.  Disky Azure Data Box, přihlaste se do portálu Azure portal a vytvoření objednávky zařízení Data Box pro disky. Zadejte svoje kontaktní údaje a podrobnosti o oznámení. Po provedení objednávky vám budou disky dodány podle dostupnosti do 10 dnů.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Otázka: Jaký je maximální objem dat, který se dá přenést pomocí disků Data Box Disk v jedné instanci?
A. Při 5 discích, kdy každý má velikost 8 TB (7 TB využitelné kapacity), je maximální využitelná kapacita 35 TB. V jedné instanci je tedy možné přenést 35 TB dat. Pro přenos většího objemu dat je nutné objednat další disky.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Otázka: Jak zjistím, jestli jsou disky Data Box Disk dostupné v mojí oblasti? 
A.  Disků data Box jsou aktuálně dostupné v USA, Kanadě, Austrálii a všechny země, v rámci Evropské unie.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Otázka: Ve kterých oblastech lze data ukládat pomocí disků Data Box Disk?
A. Data Box Disk se podporuje pro všechny oblasti v USA, Kanadě, Austrálii a oblasti západní Evropa a Severní Evropa. Podporované jsou jenom oblasti veřejného cloudu Azure. Azure Government ani jiné suverénní cloudy nejsou podporované.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Otázka: Na koho se mám obrátit, když narazím na nějaké problémy s disky Data Box Disk?
A. Pokud narazíte na případné problémy s disků Data Box, [obraťte se na Microsoft Support](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Konfigurace a připojení
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Otázka: Můžu v objednávce určit počet disků Data Box Disk?
A.  Ne. Získáte disky o velikosti 8 TB (maximálně 5 disků), podle objemu vašich dat a dostupnosti disků.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Otázka: Jak disky Data Box Disk odemknu? 
A.  Na webu Azure Portal přejděte k vaší objednávce disků Data Box Disk a přejděte na **podrobnosti o zařízení**. Zkopírujte klíč. Stáhněte a rozbalte odemykací nástroj Data Box Disku z webu Azure Portal do operačního systému. Spusťte tento nástroj v počítači s daty, která chcete zkopírovat na disky. Zadejte klíč k odemknutí disků. Stejný klíč odemkne všechny disky. 

Podrobný postup najdete v článku o [odemknutí disků v klientovi pro Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) nebo o [odemknutí disků v klientovi pro Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Otázka: Můžu k připojení a zkopírování dat na disky Data Box Disk použít hostitelský počítač Linux?
A.  Ano. K připojení a zkopírování dat na Data Box Disky je možné použít klienty pro Linux i Windows. Další informace získáte, když přejdete na seznam [podporovaných operačních systémů](data-box-disk-system-requirements.md) hostitelského počítače.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Otázka: Moje disky se odeslaly, ale já teď chci tuto objednávku zrušit. Proč není dostupné tlačítko pro zrušení?
A.  Objednávku lze po vytvoření zrušit jenom do doby její expedice. Jakmile se disky odešlou, není už možné danou objednávku zrušit. Mohou však vrátit disky za poplatek. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Otázka: Můžu pro přenos dat připojit k hostitelskému počítači více disků Data Box Disk současně?
A. Ano. Pro přenos dat lze k jednomu hostitelskému počítači připojit více disků Data Box Disk a několik úloh kopírování může běžet paralelně.

## <a name="track-status"></a>Sledování stavu

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Otázka: Jak můžu sledovat disky od vytvoření objednávky až po vrácení disků? 
A.  Stav objednávky disků Data Box Disk můžete sledovat na webu Azure Portal. Při vytváření objednávky se zobrazuje také výzva k zadání e-mailové adresy pro oznámení. Pokud jste tuto adresu zadali, budete prostřednictvím e-mailu dostávat oznámení o všech změnách stavu dané objednávky. Další informace o [konfiguraci e-mailových adres pro oznámení](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>Otázka: Jak můžu disky vrátit? 
A.  Microsoft přikládá k balíčku s disky Data Box Disk expediční štítek. Připevněte tento štítek k odesílanému balíčku a zavezte zapečetěný balíček na pobočku dopravce. Pokud se tento štítek poškodil nebo ztratil, přejděte na **Přehled > Stáhnout expediční štítek** a stáhněte si nový zpětný expediční štítek.

## <a name="migrate-data"></a>Migrace dat

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Otázka: Jaká je maximální velikost dat, která se dá použít s disky Data Box Disk?  
A.  Řešení s disky Data Box Disk může mít až 5 disků s maximální využitelnou kapacitou 35 TB. Samotné disky mají velikost 8 TB (využitelných je 7 TB).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Otázka: Jaké maximální velikosti objektu blob bloku a objektu blob stránky disky Data Box Disk podporují? 
A.  Maximální velikosti se řídí omezeními služby Azure Storage. Maximální velikost objektu blob bloku je přibližně 4,768 TiB a maximální velikost objektu blob stránky je 8 TiB. Další informace najdete v tématu [Škálovatelnost a cíle výkonnosti Azure Storage](../storage/common/storage-scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Otázka: Jaká je u disků Data Box Disk rychlost přenosu dat?
A. Při testování s disky připojenými přes USB 3.0 byl výkon disku až 430 MB/s. Skutečné hodnoty se liší v závislosti na velikosti použitých souborů. U menších souborů může být výkon nižší.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Otázka: Jak zajistím, aby byla data při přenosu zabezpečená? 
A.  Disky Data Box Disk se šifrují pomocí 128bitového šifrování AES nástroje BitLocker a klíč je dostupný jenom na webu Azure Portal. Pokud chcete tento klíč získat, přihlaste se na web Azure Portal pomocí přihlašovacích údajů k účtu. Po spuštění nástroje pro odemykání disků Data Box Disk zadejte tento klíč.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Otázka: Jak můžu kopírovat data na disky Data Box Disk? 
A.  Ke kopírování dat na disky použijte nástroj pro kopírování SMB, například Robocopy, Diskboss nebo třeba i přetažení v Průzkumníkovi souborů Windows.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Otázka: Máte nějaké tipy pro zrychlení kopírování dat?
A.  Pokud chcete zrychlit proces kopírování:

- Použijte pro kopírování dat více streamů. Například v Robocopy použijte možnost více vláken. Další informace o přesný příkaz použitý, přejděte na [kurzu: Kopírování dat do Azure Data Box Disk a ověřte](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Použijte více relací.
- Místo kopírování přes sdílenou síťovou složku (při kterém vás můžou omezovat rychlosti sítí) zajistěte, aby se data nacházela přímo na počítači, ke kterému jsou příslušné disky připojené.
- Zkontrolujte, že během procesu kopírování používáte USB 3.0 nebo novější. K identifikaci řadičů USB a zařízení USB připojených k počítači si stáhněte a používejte [nástroj USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview).
- Proveďte srovnávací testy výkonu počítače, který se používá ke kopírování dat. Pro srovnávací testy výkonu hardwaru serveru si stáhněte a používejte [nástroj Bluestop FIO](https://bluestop.org/fio/).

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Otázka: Jak se dá kopírování zrychlit, když jsou zdrojová data tvořená malými soubory (o velikosti v kB nebo několika málo MB)?
A.  Pokud chcete zrychlit proces kopírování:

- Vytvořte místní disk VHDx v rychlém úložišti nebo prázdný virtuální pevný disk na disku HDD/SSD (pomalejší).
- Připojte ho k virtuálnímu počítači.
- Zkopírujte soubory na disk tohoto virtuálního počítače.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Otázka: Můžu s disky Data Box Disk používat více účtů úložiště?
A.  Ne. Pro použití s disky Data Box Disk se momentálně podporuje jenom jeden účet úložiště, obecný nebo klasický. Podporují se horké i studené objekty blob. V současné době jsou podporované jenom účty úložiště v USA, západní Evropa a Severní Evropa ve veřejném cloudu Azure.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>Otázka: Co je sada nástrojů, které jsou k dispozici pro data s datovými disky pole?
A. Sada nástrojů dostupná s datovým diskem pole obsahuje tři nástroje:
 - **Data Box Disk odemknutí nástroj**: Tento nástroj použijte k odemknutí zašifrované disky, které se dodávají od Microsoftu. Při odemykání disky pomocí nástroje, je třeba zadat klíč k dispozici v objednávka disku Data Box na webu Azure Portal. 
 - **Data Box Disk ověření nástroj**: Použití tohoto nástroje můžete ověřit, velikost, formátu a objektů blob pojmenuje podle názvy convnetions v Azure. Také vygeneruje kontrolní součty pro zkopírovaná data, která se následně použijí k ověření, nahrát data do Azure.
 - **Nástroj pro kopírování rozdělení disku pole data**: Tento nástroj použijte, pokud používáte několik disků a mají velkou datovou sadu, kterou je potřeba rozdělit a zkopírovali na všech discích. Tento nástroj je momentálně dostupná pro Windows.

Sada nástrojů je k dispozici pro Windows i Linux. Si můžete stáhnout na sadu nástrojů:
 - [Stáhnout sadu nástrojů Data Box Disk pro Windows](https://aka.ms/databoxdisktoolswin) 
 - [Stáhnout sadu nástrojů Data Box Disk pro Linux](https://aka.ms/databoxdisktoolslinux)


## <a name="verify-and-upload"></a>Ověření a nahrání

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Otázka: Za jak dlouho od vrácení disků budu mít přístup ke svým datům v Azure? 
A.  Jakmile se daná objednávka kopírování dat zobrazí jako dokončená, měli byste mít přístup k vašim datům.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Otázka: Kde v Azure se moje data po nahrání nachází?
A.  Při kopírování dat ve složkách *BlockBlob* a *PageBlob* na vašem disku se pro každou podsložku ve složkách *BlockBlob* a *PageBlob* vytvoří kontejner v účtu Azure Storage. Pokud jste zkopírovali soubory ve složkách *BlockBlob* a *PageBlob* přímo, pak se v účtu Azure Storage nachází ve výchozím kontejneru *$root*.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Otázka: Zjistil(a) jsem, že jsem nedodržel(a) požadavky Azure na názvy kontejnerů. Znamená to, že nahrání mých dat do Azure se nezdaří?
A. Pokud názvy kontejnerů obsahují velká písmena, automaticky se převedou na malá písmena. Pokud názvy nedodržují jiné požadavky (speciální znaky, ostatní jiné jazyky atd.), nahrání se nezdaří. Další informace najdete v článku o [zásadách vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions).

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

## <a name="next-steps"></a>Další postup

- Přečtěte si [systémové požadavky služby Data Box](data-box-disk-system-requirements.md).
- Seznamte se s [omezeními služby Data Box](data-box-disk-limits.md).
- Rychlé nasazení řešení [Azure Data Box Disk](data-box-disk-quickstart-portal.md) na webu Azure Portal
