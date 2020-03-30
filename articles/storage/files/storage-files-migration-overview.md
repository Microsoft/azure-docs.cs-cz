---
title: Migrace do sdílených složek Azure
description: Přečtěte si o migraci do sdílených složek Azure a najděte průvodce migrací.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247312"
---
# <a name="migrate-to-azure-file-shares"></a>Migrace do sdílených složek Azure

Tento článek popisuje základní aspekty migrace do sdílených složek Azure.

Vedle základů migrace obsahuje tento článek seznam existujících individuálních průvodců migrací. Tyto průvodce migrací vám pomohou přesunout soubory do sdílených složek Azure a jsou uspořádány podle toho, kde se vaše data nacházejí dnes a jaký model nasazení (pouze cloud nebo hybridní) plánujete přejít.

## <a name="migration-basics"></a>Základy migrace

V Azure je k dispozici několik různých typů cloudového úložiště. Základním aspektem migrace souborů do Azure je určit, která možnost úložiště Azure je vhodná pro vaše data.

Sdílené složky Azure jsou skvělé pro data souborů pro obecné účely. Opravdu vše, co používáte on-premises SMB nebo NFS podíl pro. Pomocí [Azure File Sync](storage-sync-files-planning.md)můžete volitelně uložit obsah několika sdílených složek Azure do mezipaměti na několika místních serverech Windows.

Pokud máte aplikaci, která je aktuálně spuštěná na místním serveru, může být ukládání souborů ve sdílených složkách Azure pro vás v závislosti na aplikaci to pravé. Aplikaci můžete zvednout tak, aby se spouštěla v Azure a používala sdílené složky Azure jako sdílené úložiště. Můžete také zvážit [Disky Azure](../../virtual-machines/windows/managed-disks-overview.md) pro tento scénář. Pro aplikace, které se rodí v cloudu, které nejsou závislé na SMB nebo místní přístup k jejich data nebo sdílený přístup, úložiště objektů, jako jsou [objekty blob Azure](../blobs/storage-blobs-overview.md), je často nejlepší volbou.

Klíčem v jakékoli migraci je zachytit všechny příslušné věrnost souborů při migraci souborů z jejich aktuální umístění úložiště do Azure. Pomoc při výběru správného úložiště Azure je také aspekt, kolik věrnosti podporované možností úložiště Azure a je vyžadováno vaším scénářem. Data souborů pro obecné účely tradičně závisí na metadatech souboru. Data aplikace nemusí. Soubor má dvě základní součásti:

- **Datový proud**: Datový proud souboru ukládá obsah souboru.
- **Metadata souboru**: Meta data souboru mají několik dílčích součástí:
   * Atributy souboru: Například jen pro čtení.
   * Oprávnění k souborům: Označována jako *oprávnění ntfs* nebo *soubory AKL souboru a složky*.
   * Časová razítka: Zejména *vytvořit-* a *poslední upravené-* časová razítka.
   * Alternativní datový proud: Prostor pro uložení většího množství nestandardních vlastností.

Věrnost souborů v rámci migrace lze proto definovat jako možnost ukládat všechny příslušné informace o souborech do zdroje, možnost jejich přenosu pomocí nástroje pro migraci a možnost jejich uložení v cílovém úložišti migrace.

Chcete-li zajistit, aby migrace probíhala co nejplynuleji, [určete nejlepší nástroj pro kopírování podle svých potřeb](#migration-toolbox) a přizpůsobte cíl úložiště zdroji.

Vezmeme-li v úvahu předchozí informace, je jasné, jaké je cílové úložiště pro soubory pro obecné účely v Azure: [sdílené složky Azure](storage-files-introduction.md). Ve srovnání s úložištěm objektů v objektech BLOB Azure lze metadata souborů nativně uložit do souborů ve sdílené složce Azure.

Sdílené složky Azure také zachovat hierarchii souborů a složek. Dále:
* Oprávnění ntfs lze uložit na soubory a složky, jak jsou místní.
* Uživatelé služby AD (nebo uživatelé Služby Azure AD DS) mají nativně přístup ke sdílené složce Azure. 
    Používají svou aktuální identitu a získají přístup na základě oprávnění ke sdílení, stejně jako seznamu ACL souborů a složek. Chování není na rozdíl od, když se uživatelé připojují k místní sdílené složce.
*  Alternativní datový proud je primární aspekt věrnosti souborů, který aktuálně nelze uložit do souboru ve sdílené složce Azure.
   Je zachována místně, když se jedná o Azure File Sync.

* [Další informace o ověřování ad pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md)
* [Další informace o ověřování azure active directory domain services (AAD DS) pro sdílené složky Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Průvodci migrací

V následující tabulce jsou uvedeny podrobné příručky pro migraci.

Navigace podle:
1. Vyhledejte řádek zdrojového systému, ve které jsou soubory aktuálně uloženy.
2. Rozhodněte se, jestli cílíte na hybridní nasazení, kde používáte Azure File Sync k ukládání obsahu jedné nebo více sdílených složek Azure místně, nebo jestli chcete použít sdílené složky Azure přímo v cloudu. Vyberte cílový sloupec, který odráží vaše rozhodnutí.
3. V rámci průniku zdroje a cíle jsou v buňce tabulky uvedeny dostupné scénáře migrace. Vyberte jednu z nich, chcete-li přímo propojit s podrobným průvodcem migrací.

Scénář bez odkazu ještě nemá publikovanou příručku pro migraci. V této tabulce občas zkontrolujte aktualizace. Nové příručky budou zveřejněny, jakmile budou k dispozici.

| Zdroj | Cíl: </br>Hybridní nasazení | Cíl: </br>Nasazení pouze v cloudu |
|:---|:--|:--|
| | Kombinace nástrojů:| Kombinace nástrojů: |
| Windows Server 2012 R2 a novější | <ul><li>[Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)</li><li>[Synchronizace souborů Azure + datová schránka](storage-sync-offline-data-transfer.md)</li><li>Služba migrace úložiště + synchronizace souborů Azure</li></ul> | <ul><li>Synchronizace souborů Azure</li><li>Synchronizace souborů Azure + datová schránka</li><li>Služba migrace úložiště + synchronizace souborů Azure</li><li>Robocopy</li></ul> |
| Windows Server 2012 a starší | <ul><li>Synchronizace souborů Azure + datová schránka</li><li>Služba migrace úložiště + synchronizace souborů Azure</li></ul> | <ul><li>Služba migrace úložiště + synchronizace souborů Azure</li><li>Robocopy</li></ul> |
| Úložiště připojené k síti (NAS) | <ul><li>[Synchronizace souborů Azure + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy + Synchronizace souborů Azure](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| StorJednoduché 8100 / 8600 | <ul><li>[Azure File Sync + 8020 Virtuální zařízení](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorJednoduché 1200 | <ul><li>[Synchronizace souborů Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Panel nástrojů migrace

### <a name="file-copy-tools"></a>Nástroje pro kopírování souborů

K dispozici je několik nástrojů pro kopírování souborů od společnosti Microsoft a jiných společností. Chcete-li vybrat správný nástroj pro kopírování souborů pro váš scénář migrace, je třeba zvážit tři základní otázky:

* Podporuje nástroj pro kopírování zdroj a cílové umístění pro danou kopii souboru? 
    * Podporuje vaši síťovou cestu nebo dostupné protokoly (například REST/SMB/NFS) do a ze zdrojových a cílových úložišť?
* Zachová nástroj pro kopírování potřebnou věrnost souborů, která je podporována zdrojovým nebo cílovým umístěním? V některých případech cílové úložiště nepodporuje stejnou věrnost jako váš zdroj. Již jste se rozhodli, že cílové úložiště je dostatečné pro vaše potřeby, proto nástroj pro kopírování musí odpovídat pouze možnosti věrnosti souborů cílů.
* Obsahuje nástroj pro kopírování funkce, které zapadají do strategie migrace? 
    * Zvažte například, zda má možnosti, které umožňují minimalizovat prostoje. Dobrá otázka, kterou se zeptat, je: Mohu spustit tuto kopii vícekrát na stejném místě, uživateli aktivně přistupovat k umístění? Pokud ano, můžete výrazně snížit množství prostojů. Porovnejte to se situací, kdy můžete spustit kopii pouze tehdy, když se zdroj přestane měnit, aby byla zaručena úplná kopie.

Následující tabulka klasifikuje nástroje Microsoftu a jejich aktuální vhodnost pro sdílené složky Azure:

| Doporučené | Nástroj | Podporuje sdílené složky Azure | Zachová věrnost souboru. |
| :-: | :-- | :---- | :---- |
|![Ano, doporučujeme.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Podporuje se. Sdílené složky Azure lze připojit jako síťové jednotky. | Plná věrnost* |
|![Ano, doporučujeme.](media/storage-files-migration-overview/circle-green-checkmark.png)| Synchronizace souborů Azure | Nativně integrované do sdílených složek Azure. | Plná věrnost* |
|![Ano, doporučujeme.](media/storage-files-migration-overview/circle-green-checkmark.png)| Služba migrace úložiště (SMS) | Nepřímo podporováno. Sdílené složky Azure lze připojit jako síťové jednotky na serveru SMS target. | Plná věrnost* |
|![Není zcela doporučeno.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Datový box Azure | Podporuje se. | Nekopíruje metadata. [Lze použít v kombinaci s Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Nedoporučuje se.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Podporuje se. | Nekopíruje metadata. |
|![Nedoporučuje se.](media/storage-files-migration-overview/circle-red-x.png)| Azure Storage Explorer | Podporuje se. | Nekopíruje metadata. |
|![Nedoporučuje se.](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Podporuje se. | Nekopíruje metadata. |
|||||

*\*Plná věrnost: splňuje nebo překračuje možnosti sdílení souborů Azure.*

### <a name="migration-helper-tools"></a>Pomocné nástroje pro migraci

V této části jsou uvedeny nástroje, které pomáhají plánovat a provádět migrace.

* **RoboCopy, od společnosti Microsoft Corporation**

    Jeden z nejpoužitelnějších nástrojů pro kopírování pro migraci souborů je součástí systému Microsoft Windows. Vzhledem k mnoha možnostem v tomto nástroji je hlavní [dokumentace RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) užitečným zdrojem.

* **TreeSize, od společnosti JAM Software GmbH**

    Azure File Sync se primárně škáluje podle počtu položek (souborů a složek) a méně s celkovou částkou TiB. Tento nástroj lze použít k určení počtu souborů a složek na svazcích systému Windows Server. Kromě toho jej lze použít k vytvoření perspektivy před [nasazením Synchronizace souborů Azure](storage-sync-files-deployment-guide.md) – ale také po, když je zapojeno vrstvení cloudu a chcete vidět nejen počet položek, ale také ve kterých adresářích se vaše serverová mezipaměť používá nejvíce.
    Tento nástroj (testovaná verze 4.4.1) je kompatibilní s cloudovými vrstvenými soubory. Nezpůsobí odvolání vrstvených souborů během jeho normální operace.


## <a name="next-steps"></a>Další kroky

1. Vytvořte plán, pro který nasazení sdílených složek Azure (pouze cloud nebo hybridní) se snažíte.
2. Projděte si seznam dostupných průvodců migrací a najděte podrobný průvodce, který odpovídá vašemu zdroji a nasazení sdílených složek Azure.

Další informace o technologiích Azure Files uvedených v tomto článku:

* [Přehled sdílení souborů Azure](storage-files-introduction.md)
* [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
* [Synchronizace souborů Azure: Vrstvení cloudu](storage-sync-cloud-tiering.md)
