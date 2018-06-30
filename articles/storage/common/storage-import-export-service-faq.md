---
title: Nejčastější dotazy týkající se služby Azure Import/Export | Microsoft Docs
description: Přečtěte si odpovědi na časté otázky týkající se služby Azure Import exportovat.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.openlocfilehash: d324025a50346415de8232af4adc487a5d907331
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031392"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Služba Azure Import/Export: Nejčastější dotazy 
Dále jsou otázky a odpovědi, které mohou mít při použití služby Azure Import/Export k přenosu dat do úložiště Azure. Otázky a odpovědi jsou uspořádány do následujících kategorií:

- O službě Import/Export
- Příprava disky pro import a export
- Import a Export úloh
- Přesouvání disků
- Různé 

## <a name="about-importexport-service"></a>O službě Import/Export

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Můžete zkopírovat pomocí služby Azure Import/Export úložiště Azure File?

Ano. Podporuje služba Azure Import/Export importovat do Azure File Storage. Export souborů Azure v tuto chvíli nepodporuje.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Je dostupná pro předplatná CSP služba Azure Import/Export?

Ano. Služba Azure Import/Export podporuje předplatných cloudové řešení zprostředkovatelé (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Můžete použít službu Azure Import/Export pro kopírování PST poštovní schránky a dat služby SharePoint k O365?

Ano. Další informace, přejděte na [Import PST souborů nebo dat služby SharePoint do služeb Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Můžete použít službu Azure Import/Export pro kopírování Moje zálohování offline ke službě Azure Backup?

Ano. Další informace, přejděte na [pracovní postup Offline zálohování v Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Můžete zakoupit jednotky pro úlohy importu a exportu od společnosti Microsoft?

Ne. Musíte dodávat vlastní jednotky pro import a export úloh.


## <a name="preparing-disks-for-importexport"></a>Příprava disky pro import a export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Můžete přeskočit krok přípravy jednotky pro úlohy importu? Můžete připravit na jednotku bez kopírování?

Ne. Všechny jednotky, které používají k importu dat musí být připravený pomocí nástroje Azure WAImportExport. Pomocí nástroje taky zkopírovat data do jednotky.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Je potřeba provést jakékoli přípravu disku při vytváření úlohy exportu?

Ne. Některé prechecks se nedoporučuje. Chcete-li zkontrolovat počet disků, které vyžaduje, použijte příkaz PreviewExport WAImportExport nástroj. Další informace najdete v tématu [náhled jednotka využití pro úlohu exportovat](https://msdn.microsoft.com/library/azure/dn722414.aspx). Příkaz umožňuje zobrazit náhled využití disku pro vybrané objekty BLOB, na základě velikosti jednotky, které chcete použít. Také zkontrolujte, zda lze číst z a zapisovat na pevný disk, který je dodáván pro úlohu export.

## <a name="importexport-jobs"></a>Import a Export úloh

### <a name="can-i-cancel-my-job"></a>Můžete zrušit má úloha?
Ano. Úlohy můžete zrušit, pokud je jeho stav **vytváření** nebo **přesouvání**. Kromě těchto fázích úlohu nelze zrušit, a pokračuje až závěrečná fáze.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak dlouho na portálu Azure můžete zobrazit stav dokončené úlohy?
Můžete zobrazit stav pro dokončené úlohy po dobu 90 dnů. Dokončené úlohy jsou odstraněny po 90 dnech.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Pokud chcete importovat nebo exportovat víc než 10 jednotky, co mám dělat?
Jeden import nebo export úloha může odkazovat jenom 10 jednotky v rámci jedné úlohy. Pro odeslání víc než 10 jednotky, měli byste vytvořit více úloh. Jednotky, které jsou spojené s úlohou stejné musí dodávané společně ve stejném balíčku. Pro další informace a pokyny, když kapacity dat zahrnuje více disku importovat úlohy, obraťte se na společnost Microsoft na bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>Přesouvání disků

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Jaký je maximální počet HDD pro v jedné dodávky?
Neexistuje žádné omezení počtu pevných disků v jedné dodávky. Pokud disky patří do více úloh, doporučujeme vám: 
- označení disky s odpovídající název úlohy.
- Aktualizace úlohy s číslem sledování na konci -1,-2 atd.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>By měla obsahovat jakoukoli jinou hodnotu než na pevný disk v mé balíčku?
V balíčku přesouvání dodávat pouze pevné disky. Nezahrnujte věci, jako je napájecích kabelů napájení nebo kabely USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Je nutné dodávat Moje jednotky pomocí FedEx nebo DHL?
Můžete zaslat jednotek pro datové centrum Azure pomocí žádné známé poskytovatel jako FedEx DHL, UPS nebo nám poštovní služby. Pro vratky jednotek pro vás z datového centra, je nutné zadat však:

- Číslo účtu FedEx v USA a EU, nebo
- Číslo účtu DHL v Asii a Austrálie oblastech.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Existují nějaká omezení s přesouvání mezinárodní úrovni svou jednotku?
Upozorňujeme, že fyzická média, která jsou přesouvání muset křížové mezinárodní hranice. Jste zodpovědní za zajištění, vaše fyzická média a data jsou importovat a exportovat v souladu s platné zákony. Před jejich odesláním fyzických médií, zkontrolujte s vaší poradci ověřit, jestli se médiu a dat můžete souladu s právem odeslaná do identifikovaného datového centra. To vám pomůže zajistit, aby obdržel Microsoft včas.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Při vytváření úlohy, adresy příjemce je umístění, které se liší od umístění účtu úložiště. Co bych měl/a dělat?

Některé umístění účtu úložiště jsou namapované na alternativní přesouvání umístění. Dříve dostupná přenosů umístění můžete také dočasně mapovat do alternativního umístění. Vždy zkontrolujte přenosů adresa zadaná při vytváření úlohy před přesouvání jednotky.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Při přesouvání svou jednotku, poskytovatel požádá o data center adresa a telefonní číslo kontaktu. Co by měl poskytovat?

Řadič domény a telefonní číslo adresy se poskytuje jako součást úlohy vytvoření.


## <a name="miscellaneous"></a>Různé

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co se stane, když I omylem poslat pevný disk, který není v souladu s požadavky na podporované?

Datového centra Azure vrátí na jednotku, která není v souladu s požadavky na podporované pro vás. Pokud jenom některé jednotky v balíčku splnit požadavky na podporu, tyto jednotky se zpracují a jednotky, které nesplňují požadavky na bude vrácen vám.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Službu formátu jednotky před vrácením je?

Ne. Všechny jednotky jsou šifrované pomocí Bitlockeru.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak můžete přistupovat k datům, importovaných pomocí této služby?

Použití portálu Azure nebo [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) pro přístup k datům v rámci účtu úložiště Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Po dokončení importu co Moje data vypadat v účtu storage? Uchování Moje hierarchie adresářů

Při přípravě na pevný disk pro úlohy importu, je zadána cílového pole DstBlobPathOrPrefix v datové sadě sdíleného svazku clusteru. Toto je cílový kontejner v účtu úložiště, ke kterému se zkopíruje data z pevného disku. V tomto kontejneru cílové virtuální adresáře jsou vytvořeny pro složky z pevného disku a objekty BLOB jsou vytvořené pro soubory. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Pokud má jednotka souborů, které již existují v svůj účet úložiště, služba přepsat existující objekty BLOB nebo soubory?

Závisí. Při přípravě na jednotku, můžete určit, zda by dojít k přepsání souborů cílové nebo ignorováno pomocí pole v souboru CSV datovou sadu názvem dispozice: < přejmenovat | přepsat ne | přepsat >. Ve výchozím nastavení služba přejmenuje nové soubory a nikoli přepsat existující objekty BLOB nebo soubory.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Je nástroj WAImportExport kompatibilní s 32bitové operační systémy?
Ne. Nástroj WAImportExport je jenom kompatibilní s operačními systémy Windows 64-bit. Úplný seznam podporované operačního systému, přejděte na [podporované operační systémy](). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Jaký je maximální objekt Blob bloku a velikost objektu Blob stránky podporovány Azure Import/Export?

Objekt Blob bloku maximální velikost je přibližně 4.768TB nebo 5 000 000 MB.
Objekt Blob stránky maximální velikost je 1TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Podporuje Azure Import/Export šifrování AES 256?
Služba Azure Import/Export používá ve výchozím nastavení nástroj bitlocker šifrování AES-128. Toto můžete změnit na AES 256 ručně šifrování pomocí nástroje bitlocker, než budou data zkopírována. 

- Pokud používáte [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), zde je ukázka příkazu
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Pokud používáte [WAImportExport V2](https://www.microsoft.com/en-us/download/details.aspx?id=55280) zadejte "AlreadyEncrypted" a zadejte klíče v driveset sdíleného svazku clusteru.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Další postup

* [Co je Azure Import/Export?](storage-import-export-service.md)


