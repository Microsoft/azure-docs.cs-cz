---
title: Nejčastější dotazy ke službě Azure Import/Export | Dokumenty společnosti Microsoft
description: Přečtěte si odpovědi na nejčastější dotazy týkající se služby Export importu Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 904f93aa705a4e327f29bbec109bdf3b937f6c70
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519488"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Služba Azure Import/Export: nejčastější dotazy

Následují otázky a odpovědi, které můžete mít při použití služby Azure Import/Export k přenosu dat do úložiště Azure. Otázky a odpovědi jsou uspořádané do těchto kategorií:

- O službě Import/Export
- Příprava disků pro import a export
- Úlohy importu a exportu
- Přepravní disky
- Různé

## <a name="about-importexport-service"></a>O službě Import/Export

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Můžu zkopírovat úložiště souborů Azure pomocí služby Azure Import/Export?

Ano. Služba Import a export Azure podporuje import do úložiště souborů Azure. V tuto chvíli nepodporuje export souborů Azure.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Je služba Import a export Azure dostupná pro předplatná CSP?

Ano. Služba Azure Import/Export podporuje předplatná zprostředkovatelů cloudových řešení (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Můžu ke kopírování poštovních schránek PST a sharepointových dat do O365 použít službu Import a Export Azure?

Ano. Další informace najdete [v části Import souborů PST nebo sharepointových dat do Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Můžu použít službu Azure Import/Export ke kopírování záloh offline do služby Azure Backup Service?

Ano. Další informace najdete [v pracovním postupu Offline zálohování v azure backup .](../../backup/backup-azure-backup-import-export.md)

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Mohu zakoupit jednotky pro importace a export úlohod microsoftu?

Ne. Musíte dobýt vlastní disky pro import a export úloh.

## <a name="preparing-disks-for-importexport"></a>Příprava disků pro import a export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Je možné přeskočit krok přípravy jednotky pro úlohu importu? Mohu připravit disk bez kopírování?

Ne. Všechny jednotky použité k importu dat musí být připraveny pomocí nástroje Azure WAImportExport. Pomocí nástroje můžete také kopírovat data na jednotku.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Musím při vytváření úlohy exportu provádět přípravu disku?

Ne. Doporučujeme některé předběžné kontroly. Chcete-li zkontrolovat požadovaný počet disků, použijte příkaz NáhledExport nástroje WAImportExport. Další informace naleznete v [tématu Náhled využití jednotky pro úlohu exportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Příkaz vám pomůže zobrazit náhled využití jednotky pro vybrané objekty BLOB na základě velikosti jednotek, které budete používat. Zkontrolujte také, zda můžete číst z pevného disku, který je dodáván pro úlohu exportu, a zapisovat jej.

## <a name="importexport-jobs"></a>Úlohy importu a exportu

### <a name="can-i-cancel-my-job"></a>Můžu zrušit svou práci?

Ano. Úlohu můžete zrušit, pokud je její stav **Vytvoření** nebo **Odeslání**. Kromě těchto fází nelze úlohu zrušit a pokračovat až do závěrečné fáze.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak dlouho můžu na webu Azure Portal zobrazit stav dokončených úloh?

Můžete zobrazit stav dokončených úloh po dobu až 90 dnů. Dokončené úlohy jsou odstraněny po 90 dnech.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Co mám dělat, když chci importovat nebo exportovat více než 10 jednotek?

Jedna úloha importu nebo exportu může odkazovat pouze na 10 jednotek v jedné úloze. Chcete-li dobýt více než 10 jednotek, měli byste vytvořit více úloh. Jednotky přidružené ke stejné úloze musí být dodány společně ve stejném balíčku.
Další informace a pokyny v případě, že kapacita dat zahrnuje více úloh importu disku, obraťte se na podporu společnosti Microsoft.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Nahraný objekt blob zobrazuje stav jako "Zapůjčení vypršelo". Co bych měl/a dělat?

Pole Vypršela platnost zapůjčení. Import nebo export trvá zapůjčení objektu blob během nahrávání a ujistěte se, že žádný jiný proces můžete aktualizovat objekt blob paralelně. Platnost zapůjčení znamená, že import nebo export se do něj už nenahrává a objekt blob je k dispozici pro vaše použití.

## <a name="shipping-disks"></a>Přepravní disky

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Jaký je maximální počet pevných disků v jedné zásilce?

Počet pevných disků v jedné zásilce není nijak omezen. Pokud disky patří do více úloh, doporučujeme:

- označte disky odpovídajícími názvy úloh.
- aktualizovat úlohy se sledovacím číslem s upevněným -1, -2 atd.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Mám do balíčku zahrnout něco jiného než hdd?

Do přepravního balíčku odesílejte pouze pevné disky. Nezahrnejte položky, jako jsou napájecí kabely nebo kabely USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Musím své disky odesílat pomocí fedexu nebo dhl?

Disky můžete do datového centra Azure odesílat pomocí libovolného známého operátora, jako je FedEx, DHL, UPS nebo US Postal Service. Pro vrácení zásilky jednotek z datového centra je však nutné zadat:

- Číslo účtu FedEx v USA a EU nebo
- Číslo účtu DHL v oblastech Asie a Austrálie.

> [!NOTE]
> Datová centra v Indii vyžadují prohlášení dopis na hlavičkový papír (doručení challan) vrátit jednotky. Chcete-li zajistit požadovaný vstupní průkaz, musíte si také rezervovat vyzvednutí u vybraného operátora a sdílet podrobnosti s datovým centrem.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Existují nějaká omezení s dopravou a vrácením mého pohonu v mezinárodním měřítku?

Vezměte prosím na vědomí, že fyzická média, která odesíláte, mohou vyžadovat překročení mezinárodních hranic. Nesete odpovědnost za to, že vaše fyzická média a data budou importována a/nebo exportována v souladu s platnými zákony. Před odesláním fyzického média se obraťte na své poradce a ověřte, zda mohou být vaše média a data legálně odeslána do identifikovaného datového centra. To pomůže zajistit, že se dostane microsoft včas.

Po dokončení nahrávání může proces vrácení jednotek na mezinárodní adresu trvat déle než typické 2-3 dny potřebné pro místní přepravu. Během fáze uvedené na portálu Azure jako balení tým data boxů zajišťuje, že je k dispozici správná dokumentace, aby bylo zajištěno, že zásilka splňuje různé mezinárodní požadavky na import a export.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Existují nějaké zvláštní požadavky na doručení disků do datového centra?

Požadavky závisí na konkrétní omezení datového centra Azure.

- Existuje několik webů, jako je Austrálie, Německo a jih Velké Británie, které vyžadují, aby se na parcelu z bezpečnostních důvodů zapisovalo číslo Příchozí id datového centra společnosti Microsoft. Před odesláním jednotek nebo disků do datového centraadbops@microsoft.comkontaktujte Azure DataBox Operations ( ) a získejte toto číslo. Bez tohoto čísla bude balíček odmítnut.
- Datová centra v Indii vyžadují osobní údaje řidiče, například vládní průkaz nebo číslo dokladu. (například PAN, AADHAR, DL), jméno, kontakt a číslo poznávací značky vozidla, abyste získali vstupní propustku brány. Chcete-li se vyhnout zpoždění dodávek, informujte o těchto požadavcích svého operátora.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Při vytváření úlohy je dodací adresa místem, které se liší od umístění účtu úložiště. Co bych měl/a dělat?

Některá umístění účtů úložiště jsou mapována na alternativní místa expedice. Dříve dostupná místa expedice lze také dočasně mapovat na alternativní místa. Před odesláním disků vždy zkontrolujte dodací adresu poskytnutou při vytváření úloh.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Při odesílání disku se dopravce zeptá na kontaktní adresu datového centra a telefonní číslo. Co bych měl poskytnout?

Telefonní číslo a adresa dc je poskytována jako součást vytváření pracovních míst.

## <a name="miscellaneous"></a>Různé

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co se stane, když omylem odešlu pevný disk, který nevyhovuje podporovaným požadavkům?

Datové centrum Azure vrátí disk, který nevyhovuje podporovaným požadavkům. Pokud pouze některé jednotky v balíčku splňují požadavky na podporu, budou tyto jednotky zpracovány a jednotky, které nesplňují požadavky, budou vráceny.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Naformátuje služba jednotky před jejich vrácením?

Ne. Všechny jednotky jsou šifrovány nástrojem BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak lze získat přístup k datům importovaným touto službou?

Pomocí portálu Azure nebo [Průzkumníka úložiště](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) můžete přistupovat k datům v rámci účtu úložiště Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Jak budou data po dokončení importu vypadat v účtu úložiště? Je hierarchie adresářů zachována?

Při přípravě pevného disku pro úlohu importu je cíl určen polem DstBlobPathOrPrefix v datové sadě CSV. Toto je cílový kontejner v účtu úložiště, do kterého jsou zkopírována data z pevného disku. V rámci tohoto cílového kontejneru jsou vytvořeny virtuální adresáře pro složky z pevného disku a objekty BLOB jsou vytvořeny pro soubory.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Pokud jednotka obsahuje soubory, které již existují v účtu úložiště, přepíše služba existující objekty BLOB nebo soubory?

Závisí. Při přípravě jednotky můžete určit, zda mají být cílové soubory přepsány nebo ignorovány pomocí pole v souboru CSV datové sady s názvem Dispozice:<přejmenovat|nepřepsání|přepsat>. Ve výchozím nastavení služba přejmenuje nové soubory, nikoli přepsat existující objekty BLOB nebo soubory.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Je nástroj WAImportExport kompatibilní s 32bitovými operačními systémy?

Ne. Nástroj WAImportExport je kompatibilní pouze s 64bitovými operačními systémy Windows. Úplný seznam podporovaného operačního systému naleznete v [části Podporované operační systémy](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Co je maximální velikost objektu blob bloku a velikost objektu blob stránky podporovaná importem a exportem Azure?

- Maximální velikost objektu blob bloku je přibližně 4,768 TB nebo 5 000 000 MB.
- Maximální velikost objektu blob stránky je 8 TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Podporuje Azure Import/Export šifrování AES-256?

Ne. Služba Azure Import/Export používá šifrování nástroje AES-128 BitLocker.

## <a name="next-steps"></a>Další kroky

* [Co je služba Azure Import/Export?](storage-import-export-service.md)
