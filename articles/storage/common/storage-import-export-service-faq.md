---
title: Nejčastější dotazy ke službě Azure import/export | Microsoft Docs
description: Přečtěte si odpovědi na nejčastější dotazy týkající se služby Azure import export.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 08c105bd8ae9b5e0667ef5279e6c9484c631bd48
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399036"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Služba import/export v Azure: nejčastější dotazy

Níže najdete otázky a odpovědi, které můžete mít, když použijete službu Azure import/export k přenosu dat do služby Azure Storage. Otázky a odpovědi jsou uspořádané do těchto kategorií:

- O službě Import/export
- Příprava disků pro import a export
- Úlohy importu/exportu
- Jednotky pro expedici
- Různé

## <a name="about-importexport-service"></a>O službě Import/export

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Můžu zkopírovat službu Azure File Storage pomocí služby Azure import/export?

Ano. Služba Azure import/export podporuje import do Azure File Storage. V tuto chvíli nepodporuje export souborů Azure.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Je k dispozici služba Azure import/export pro předplatné CSP?

Ano. Služba import/export pro Azure podporuje odběry Cloud Solution Provider (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-microsoft-365"></a>Můžu použít službu import/export Azure ke kopírování poštovních schránek PST a dat SharePointu do Microsoft 365?

Ano. Další informace najdete v článku [Přehled importu souborů PST vaší organizace](/microsoft-365/compliance/importing-pst-files-to-office-365).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Můžu použít službu import/export Azure ke kopírování záloh do offline služby Azure Backup?

Ano. Další informace najdete [v pracovní postup offline zálohování v Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Můžu koupit jednotky pro úlohy importu/exportu od Microsoftu?

No. Pro úlohy importu a exportu musíte dodávat vlastní jednotky.

## <a name="preparing-disks-for-importexport"></a>Příprava disků pro import a export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Můžu pro úlohu importu přeskočit krok přípravy jednotky? Můžu připravit jednotku bez kopírování?

No. Libovolná jednotka používaná k importu dat musí být připravená pomocí nástroje Azure WAImportExport Tool. Pomocí tohoto nástroje můžete také kopírovat data na disk.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Potřebuji při vytváření úlohy exportu provést přípravu na disk?

No. Doporučuje se provést několik předkontrol. Pokud chcete zjistit počet požadovaných disků, použijte příkaz PreviewExport nástroje WAImportExport. Další informace najdete v tématu [zobrazení náhledu využití jednotky u úlohy exportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Příkaz vám pomůže zobrazit náhled využití jednotky pro vybrané objekty blob, a to na základě velikosti jednotek, které budete používat. Také ověřte, zda můžete číst a zapisovat na pevný disk, který je dodán pro úlohu exportu.

## <a name="importexport-jobs"></a>Úlohy importu/exportu

### <a name="can-i-cancel-my-job"></a>Můžu tuto úlohu zrušit?

Ano. Při **vytváření** nebo **přesouvání**stavu můžete úlohu zrušit. Mimo tyto fáze nelze úlohu zrušit a pokračuje do konečné fáze.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak dlouho můžu zobrazit stav dokončených úloh v Azure Portal?

Můžete zobrazit stav dokončených úloh po dobu až 90 dní. Dokončené úlohy se odstraní po 90 dnech.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Pokud chci importovat nebo exportovat více než 10 jednotek, co mám dělat?

Jedna úloha importu nebo exportu může na jednu úlohu odkazovat pouze na 10 jednotek. K dodávání více než 10 jednotek byste měli vytvořit více úloh. Jednotky přidružené ke stejné úloze se musí odeslat společně ve stejném balíčku.
Další informace a pokyny, pokud datová kapacita zahrnuje více úloh importu disků, získáte od podpora Microsoftu.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Nahraného objektu BLOB zobrazuje stav "zapůjčení vypršelo". Co bych měl/a dělat?

Můžete ignorovat pole zapůjčení vypršelo. Import/export během nahrávání bere v objektu BLOB zapůjčení, aby bylo zajištěno, že žádný jiný proces nemůže současně aktualizovat objekt BLOB. Vypršení platnosti zapůjčení znamená, že se do něj již neodesílají import/export a objekt BLOB je k dispozici pro vaše použití.

## <a name="shipping-disks"></a>Jednotky pro expedici

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Jaký je maximální počet HDD v jedné zásilce?

Počet HDD v jedné zásilce není nijak omezený. Pokud disky patří do více úloh, doporučujeme:

- Označte disky pomocí odpovídajících názvů úloh.
- aktualizuje úlohy s číslem sledování s příponou-1,-2 atd.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Mám do balíčku zahrnout cokoli jiného než HDD?

Dodejte pouze pevné disky do expedičního balíčku. Nezahrnujte položky jako kabely napájení nebo kabely USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Musím dodávat své jednotky pomocí FedEx nebo DHL?

Jednotky můžete dodávat do datacentra Azure pomocí libovolného známého nosiče, jako je FedEx, DHL, UPS nebo poštovní služba USA. Pokud ale chcete vrátit zpět odeslání jednotek z datového centra, musíte zadat následující:

- Číslo účtu FedEx v USA a EU nebo
- Číslo účtu DHL v oblastech Asie a Austrálie.

> [!NOTE]
> Datacentra v Indii vyžadují, aby se na hlavičkovém panelu (Delivery Challan) vrátilo prohlášení, které jednotky vrátí. Chcete-li uspořádat požadované zadání Pass, je nutné také zarezervovat vybraný operátor a sdílet podrobnosti s datovým centrem.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Existují nějaká omezení s expedicí a vracím mezinárodně svou jednotku?

Počítejte s tím, že fyzické médium, které budete dodávat, může být potřeba pro různé mezinárodní hranice. Zodpovídáte za to, abyste měli jistotu, že se vaše fyzická média a data importují nebo exportují v souladu s platnými zákony. Před odesláním fyzických médií se poraďte se svými poradci a ověřte, že vaše média a data lze právně dodávat do identifikovaného datového centra. To vám pomůže zajistit včas, aby se zajistila společnost Microsoft.

Po dokončení nahrávání může proces vrácení jednotek na mezinárodní adresu trvat déle, než je obvyklé 2-3 dní potřebných pro místní expedici. Během fáze uvedené ve Azure Portal jako balení Data Box tým zajišťuje, aby byla zajištěna správná dokumentace, která zajistí, že dodávka bude v souladu s různými mezinárodními požadavky na Import a export.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Existují zvláštní požadavky na poskytování mých disků do datacentra?

Požadavky závisí na konkrétním omezení datacentra Azure.

- K dispozici je několik lokalit, jako je Austrálie, Německo a Velká Británie – jih, které vyžadují, aby se na parcele zapsalo číslo příchozího ID pro Microsoft datacenter z bezpečnostních důvodů. Před odesláním jednotek nebo disků do datového centra kontaktujte Azure DataBox Operations ( adbops@microsoft.com ) a získejte toto číslo. Bez tohoto čísla se balíček odmítne.
- Datová centra v Indii vyžadují osobní údaje ovladače, jako je například karta státní správy nebo č. ověření. (například PAN, AADHAR, DL), jméno, kontakt a číslo desky auta pro získání průchodu položky brány. Aby nedošlo k prodlevám při doručování, informujte svého dopravce o těchto požadavcích.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Při vytváření úlohy je doručovací adresa umístění, které se liší od umístění svého účtu úložiště. Co bych měl/a dělat?

Některá umístění účtu úložiště jsou namapována na alternativní umístění expedice. Dříve dostupná umístění expedice mohou být také dočasně mapována na alternativní umístění. Před odesláním jednotek vždy kontrolovat doručovací adresu poskytnutou během vytváření úlohy.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Při expedici jednotky se v nosiči zobrazí kontaktní adresa a telefonní číslo datového centra. Co mám zadat?

Telefonní číslo a adresa řadiče domény jsou k dispozici v rámci vytváření úlohy.

## <a name="miscellaneous"></a>Různé

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co se stane, když omylem odešlem pevný disk, který nedodržuje podporované požadavky?

Datové centrum Azure vrátí jednotku, která nedodržuje podporované požadavky. Pokud pouze některé jednotky v balíčku splňují požadavky na podporu, budou tyto jednotky zpracovány a jednotky, které požadavky nesplňují, budou vráceny.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Naformátuje služba jednotky před jejich vrácením?

No. Všechny jednotky jsou šifrovány pomocí nástroje BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak mohu získat přístup k datům, která jsou importována touto službou?

Pro přístup k datům v rámci vašeho účtu úložiště Azure použijte Azure Portal nebo [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) .  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Jak budou data po dokončení importu vypadat jako v účtu úložiště? Je moje hierarchie adresáře zachovaná?

Když připravujete pevný disk pro úlohu importu, určí se cíl v poli DstBlobPathOrPrefix v souboru CSV sady dat. Toto je cílový kontejner v účtu úložiště, do kterého se zkopírují data z pevného disku. V rámci tohoto cílového kontejneru se vytvoří virtuální adresáře pro složky z pevného disku a objekty BLOB se vytvoří pro soubory.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Pokud má jednotka soubory, které už existují v účtu úložiště, služba přepíše existující objekty blob nebo soubory?

Závislosti. Při přípravě jednotky můžete určit, jestli se mají cílové soubory přepsat nebo ignorovat pomocí pole v souboru CSV s datovou sadou s názvem Disposition: <přejmenovat | No-Overwrite | přepsat>. Ve výchozím nastavení služba přejmenuje nové soubory místo přepsání existujících objektů BLOB nebo souborů.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Je nástroj WAImportExport kompatibilní s 32 operačními systémy?

No. Nástroj WAImportExport je kompatibilní jenom s 64 operačními systémy Windows. Úplný seznam podporovaného operačního systému najdete v části [podporované operační systémy](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Jaký je maximální velikost objektů blob bloku a velikosti objektu blob stránky podporované službou Azure import/export?

- Maximální velikost objektu blob bloku je přibližně 4.768 TB nebo 5 000 000 MB.
- Maximální velikost objektu blob stránky je 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Podporuje Azure import/export šifrování AES-256?

Ano. Služba import/export v Azure používá šifrování AES-256 BitLockeru.

## <a name="next-steps"></a>Další kroky

* [Co je služba Azure Import/Export?](storage-import-export-service.md)
