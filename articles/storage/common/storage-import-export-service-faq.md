---
title: Nejčastější dotazy týkající se služby Azure Import/Export | Dokumentace Microsoftu
description: Přečtěte si odpovědi na nejčastější dotazy týkající se služby Azure Import Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 6a2d067625e96aefdcb0197e1abb13b0c41fbe7f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521434"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Služba Azure Import/Export: Nejčastější dotazy 
Níže jsou dotazy a odpovědi, které můžou mít při použití služby Azure Import/Export pro přenos dat do služby Azure storage. Otázky a odpovědi jsou uspořádané do těchto kategorií:

- O službě Import/Export
- Příprava disky pro import/export
- Úlohy importu/exportu
- Přesouvání disků
- Různé 

## <a name="about-importexport-service"></a>O službě Import/Export

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Můžete zkopírovat pomocí služby Azure Import/Export Azure File storage?

Ano. Podporuje služba Azure Import/Export, import do Azure File Storage. Export souborů Azure v tuto chvíli nepodporuje.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Služba Azure Import/Export je dostupná pro předplatná CSP?

Ano. Služba Import/Export Azure podporuje předplatná poskytovatele CSP (Cloud Solution).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Můžete použít službu Azure Import/Export pro kopírování PST poštovní schránky a data služby SharePoint k O365?

Ano. Další informace najdete v části [PST importovat soubory nebo data služby SharePoint pro Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Můžete použít službu Azure Import/Export kopírování offline zálohování do služby Azure Backup?

Ano. Další informace najdete v části [pracovní postup Offline zálohování ve službě Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Je možné zakoupit jednotky pro úlohy import/export od Microsoftu?

Ne. Budete muset zasílejte vlastní disky pro import a export úloh.


## <a name="preparing-disks-for-importexport"></a>Příprava disky pro import/export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Můžete přeskočit krok přípravy disku pro úlohu importu Můžete připravit na jednotku bez kopírování?

Ne. Všechny jednotky použít pro import dat musí být připravena pomocí nástroje Azure WAImportExport. Pomocí nástroje také kopírovat data na disk.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Je nutné provést přípravu jakékoli disku při vytvoření úlohy exportu?

Ne. Některé prechecks doporučují. Pokud chcete zkontrolovat počet disků potřebný, použijte příkaz PreviewExport WAImportExport nástroj. Další informace najdete v tématu [náhled využití disku pro úlohu exportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Tento příkaz umožňuje zobrazit náhled využití disku pro vybrané objekty BLOB na základě velikosti jednotky, které se chystáte používat. Také zkontrolujte, jestli může číst z a zapisovat na pevný disk, který je dodáván pro úlohu exportu.

## <a name="importexport-jobs"></a>Úlohy importu/exportu

### <a name="can-i-cancel-my-job"></a>Můžete zrušit Moje úloha?
Ano. Úlohy můžete zrušit, pokud je jeho stav **vytváření** nebo **přesouvání**. Nad rámec těchto fází úlohu nelze zrušit, a pokračuje až do konečné fáze.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak dlouho můžete zobrazit stav dokončených úloh na webu Azure Portal?
Stav dokončené úlohy můžete zobrazit až 90 dnů. Dokončené úlohy jsou odstraněny po 90 dnech.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Když chcete importovat nebo exportovat více než 10 jednotek, co mám dělat?
Jeden import nebo export úloh může odkazovat pouze na 10 jednotek v rámci jedné úlohy. K odeslání více než 10 jednotek, měli byste vytvořit několik úloh. Jednotky spojené s stejná úloha musí dodané společně ve stejném balíku. Pro úlohy importu Další informace a pokyny, když datové kapacity zahrnuje více disků, obraťte se na společnost Microsoft zde bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>Přesouvání disků

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Jaký je maximální počet pevný disk pro u jednoho dodávek?
Neexistuje žádné omezení na počet pevných disků v jedné dodávky. Pokud disky patří do více úloh, doporučujeme vám: 
- Popisek disků pomocí odpovídajících názvů úlohy.
- Aktualizace úlohy s číslem sledování doplněny -1,-2 atd.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>By měl obsahovat nic jiného než pevný disk v balíčku?
Dodávání pouze pevných disků v přesouvání balíčku. Neobsahují různé věci, třeba kabely dodávku napájení nebo kabely USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Budu muset zasílejte Moje disky pomocí FedEx nebo DHL?
Jednotky do datacentra Azure s použitím všechny známé dopravce jako FedEx, DHL, UPS nebo poštovní služba nám můžete zaslat. Pro zpáteční zásilku jednotek vám mezi datovým centrem, je nutné zadat však:

- Číslo účtu FedEx v USA a EU, nebo
- Číslo účtu DHL v oblasti Asie a Austrálie.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Existují nějaká omezení s přesouvání jednotka mezinárodním měřítku?
Mějte prosím na paměti, že fyzických médií, která se možná muset různé mezinárodní hranice země. Jste zodpovědní za zajištění, že vaše fyzická média a data jsou importovat nebo exportovat v souladu s platnými zákony. Před předáním fyzických médií, obraťte se na vaši poradci pro ověření, že vaše média a data můžete ze zákona odeslaná do identifikovaného datového centra. To vám pomůže zajistit dosáhne Microsoft včas.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Při vytváření projektu, je dodací adresu umístění, které se liší od umístění účtu úložiště. Co bych měl/a dělat?

Některé umístění účtu úložiště se mapují na přesouvání alternativního umístění. Dříve dodání dostupných umístění můžete také dočasně mapovat do alternativního umístění. Vždy zkontrolujte dodací adresu zadali při vytváření úlohy před dodáním jednotky.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Při přesouvání svou jednotku, operátorovi vyzve k zadání data center adresu a telefonní číslo kontaktu. Co mám poskytnout?

Telefonní číslo a řadič domény adresy se poskytuje jako součást vytvoření úlohy.


## <a name="miscellaneous"></a>Různé

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co se stane, když mám náhodně HDD, který není v souladu odesílají požadavky na podporované?

Datového centra Azure vrátí na jednotku, která není v souladu s požadavky na podporované pro vás. Pokud jen některé jednotky v balíčku splňovat požadavky na podporu, zpracuje těchto jednotek a vrátí se jednotky, které nesplňují požadavky na vás.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Služba formátování disků před jejich vrácením?

Ne. Všechny disky jsou šifrované pomocí Bitlockeru.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak můžou přistupovat k datům, který je importovat tuto službu?

Pomocí portálu Azure nebo [Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) pro přístup k datům v rámci vašeho účtu úložiště Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Po dokončení importu, co data vypadat Moje v účtu úložiště? Zachování Moje hierarchii adresářů

Při přípravě pevného disku pro úlohu importu, cíl určený DstBlobPathOrPrefix pole v datové sadě sdíleného svazku clusteru. Toto je cílový kontejner v účtu úložiště, do které se kopírují data z pevného disku. V tomto kontejneru cílové virtuální adresáře jsou vytvořeny pro složky z pevného disku a objekty BLOB vytvořené pro soubory. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Pokud jednotku se soubory, které již existují ve svém účtu úložiště, službu přepsat existující objekty BLOB nebo soubory?

Závisí. Při přípravě na jednotce, můžete určit, zda má se přepsat cílové soubory nebo ignorované pomocí pole v datové sadě souboru CSV se nazývá dispozice: < přejmenovat | no přepsat | přepsat >. Ve výchozím nastavení služby přejmenuje nové soubory místo přepsání existujících objektů BLOB nebo soubory.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Je nástroj WAImportExport kompatibilní s 32bitové operační systémy?
Ne. Nástroj WAImportExport je kompatibilní jenom s operační systémy Windows 64-bit. Úplný seznam podporován operační systém, přejděte na [podporované operační systémy](). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Jaký je maximální objektů Blob bloku a velikost objektu Blob stránky podporovány službou Azure Import/Export?

Objekt Blob bloku maximální velikost je přibližně 4.768TB nebo 5 000 000 MB.
Objekt Blob stránky maximální velikost je 1TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Podporuje Azure Import/Export šifrování AES-256?
Služba Import/Export Azure je ve výchozím nastavení používá nástroj bitlocker šifrování AES-128. Můžete to změnit na AES-256 ručně šifrování nástrojem bitlocker, předtím, než se data kopírují. 

- Pokud používáte [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), tady je ukázka příkazu
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Pokud používáte [WAImportExport V2](https://www.microsoft.com/en-us/download/details.aspx?id=55280) zadejte "AlreadyEncrypted" a zadejte klíč v driveset sdíleného svazku clusteru.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Další postup

* [Co je Azure Import/Export?](storage-import-export-service.md)


