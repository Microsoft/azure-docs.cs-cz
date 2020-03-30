---
title: Použití importu a exportu Azure k přenosu dat do objektů Blob Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak na webu Azure Portal vytvářet úlohy importu a exportu pro přenos dat do a z objektů Blob Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282490"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Import dat do úložiště objektů blob Azure pomocí služby Azure Import a Export

Tento článek obsahuje podrobné pokyny, jak používat službu Azure Import/Export k bezpečnému importu velkého množství dat do úložiště objektů blob Azure. Chcete-li importovat data do objektů Blob Azure, služba vyžaduje, abyste do datového centra Azure doručovali šifrované diskové jednotky obsahující vaše data.  

## <a name="prerequisites"></a>Požadavky

Před vytvořením úlohy importu pro přenos dat do úložiště objektů blob Azure pečlivě zkontrolujte a vyplňte následující seznam předpokladů pro tuto službu.
Musíte:

* Mít aktivní předplatné Azure, které lze použít pro službu Import/Export.
* Měj alespoň jeden účet Azure Storage s kontejnerem úložiště. Podívejte se na seznam [podporovaných účtů úložiště a typů úložišť pro službu Import/Export](storage-import-export-requirements.md).
  * Informace o vytvoření nového účtu úložiště najdete v [tématu Jak vytvořit účet úložiště](storage-account-create.md).
  * Informace o kontejneru úložiště najdete v najdete [v najetí do skupiny Vytvoření kontejneru úložiště](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Mít dostatečný počet disků [podporovaných typů](storage-import-export-requirements.md#supported-disks).
* Mít systém Windows s [podporovanou verzí operačního systému](storage-import-export-requirements.md#supported-operating-systems).
* Povolte nástroj BitLocker v systému Windows. Informace naleznete [v tématu Povolení nástroje BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Stáhněte si nejnovější WAImportExport verze 1](https://www.microsoft.com/download/details.aspx?id=42659) v systému Windows. Nejnovější verze nástroje obsahuje aktualizace zabezpečení, které umožňují externí ochranu pro klíč nástroje BitLocker a aktualizovanou funkci režimu odemknutí.

  * Rozbalte výchozí složku `waimportexportv1`. Například, `C:\WaImportExportV1`.
* Měj účet FedEx/DHL. Pokud chcete použít jiného operátora než FedEx/DHL, kontaktujte tým Azure Data Box Operations na adrese `adbops@microsoft.com`.  
  * Účet musí být platný, měl by mít zůstatek a musí mít možnosti vrácení zásilky.
  * Vygenerujte sledovací číslo pro úlohu exportu.
  * Každá úloha by měla mít samostatné sledovací číslo. Více úloh se stejným sledovacím číslem není podporováno.
  * Pokud nemáte účet dopravce, přejděte na:
    * [Vytvořte si účet FedEX](https://www.fedex.com/en-us/create-account.html)nebo
    * [Vytvořte si účet DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Krok 1: Příprava jednotek

Tento krok generuje soubor deníku. Soubor deníku ukládá základní informace, jako je sériové číslo jednotky, šifrovací klíč a podrobnosti o účtu úložiště.

Při přípravě jednotek proveďte následující kroky.

1. Připojte diskové jednotky k systému Windows pomocí konektorů SATA.
2. Vytvořte na každé jednotce jeden svazek NTFS. Přiřaďte svazku písmeno jednotky. Nepoužívejte přípojné body.
3. Povolte šifrování nástroje BitLocker na svazku NTFS. Pokud používáte systém Windows Server, použijte pokyny v části [Povolení nástroje BitLocker v systému Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Zkopírujte data na šifrovaný svazek. Použijte drag and drop nebo Robocopy nebo jakýkoli takový nástroj pro kopírování. Soubor deníku *( JRN*) je vytvořen ve stejné složce, ve které nástroj spouštěte.

   Pokud je jednotka uzamčena a potřebujete jednotku odemknout, postup odemknutí se může lišit v závislosti na případu použití.

   * Pokud jste přidali data do předšifrované jednotky (nástroj WAImportExport nebyl použit pro šifrování), použijte klíč Nástroje bitlockeru (zadané číselné heslo) v automaticky otevíraném okně k odemknutí jednotky.

   * Pokud jste přidali data na jednotku, která byla zašifrována nástrojem WAImportExport, odemkněte jednotku pomocí následujícího příkazu:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Otevřete powershellnebo okno příkazového řádku s oprávněními správce. Chcete-li změnit adresář na rozbalenou složku, spusťte následující příkaz:

    `cd C:\WaImportExportV1`
6. Chcete-li získat klíč nástroje BitLocker jednotky, spusťte následující příkaz:

    `manage-bde -protectors -get <DriveLetter>:`
7. Chcete-li připravit disk, spusťte následující příkaz. **V závislosti na velikosti dat to může trvat několik hodin až dní.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Soubor deníku je vytvořen ve stejné složce, do které jste nástroj spustili. Jsou také vytvořeny další dva soubory - soubor *XML* (složka, do které nástroj spouštěte) a soubor *drive-manifest.xml* (složka, ve které jsou data umístěna).

    Použité parametry jsou popsány v následující tabulce:

    |Možnost  |Popis  |
    |---------|---------|
    |/j:     |Název souboru deníku s příponou .jrn. Soubor deníku je generován na jednotku. Doporučujeme použít sériové číslo disku jako název souboru deníku.         |
    |/id:     |ID relace. Pro každou instanci příkazu použijte jedinečné číslo relace.      |
    |/t:     |Písmeno jednotky disku, který má být dodán. Například jednotka `D`.         |
    |/bk:     |Klíč nástroje BitLocker pro jednotku. Jeho číselné heslo z výstupu`manage-bde -protectors -get D:`      |
    |/srcdir:     |Písmeno jednotky disku, který má `:\`být dodán, následované písmenem . Například, `D:\`.         |
    |/dstdir:     |Název cílového kontejneru ve službě Azure Storage.         |
    |/objekt blobtype:     |Tato možnost určuje typ objektů BLOB, do kterých chcete data importovat. Pro objekty BLOB `BlockBlob` bloku je to a pro `PageBlob`objekty BLOB stránky je .         |
    |/skipwrite:     |Možnost, která určuje, že není nutné kopírovat žádná nová data a existující data na disku je třeba připravit.          |
    |/enablecontentmd5:     |Možnost, pokud je povolena, zajišťuje, že MD5 je vypočítána a nastavena jako `Content-md5` vlastnost na každém objektu blob. Tuto možnost použijte jenom `Content-md5` v případě, že chcete toto pole použít po nahrání dat do Azure. <br> Tato možnost nemá vliv na kontrolu integrity dat (ke které dochází ve výchozím nastavení). Toto nastavení prodlužuje dobu, kterou je třeba nahrát do cloudu.          |
8. Opakujte předchozí krok pro každý disk, který je třeba odeslat. Pro každé spuštění příkazového řádku je vytvořen soubor deníku s zadaným názvem.

    > [!IMPORTANT]
    > * Spolu se souborem `<Journal file name>_DriveInfo_<Drive serial ID>.xml` deníku je soubor vytvořen také ve stejné složce, ve které je nástroj umístěn. Soubor XML se používá místo souboru deníku při vytváření úlohy, pokud je soubor deníku příliš velký.

## <a name="step-2-create-an-import-job"></a>Krok 2: Vytvoření úlohy importu

Chcete-li vytvořit úlohu importu na webu Azure Portal, proveďte následující kroky.

1. Přihlaste https://portal.azure.com/se k souboru .
2. Přejděte na **Všechny služby > úlohy importu > exportu**.

    ![Přejít na úlohy importu a exportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klepněte na **tlačítko Vytvořit úlohu importu nebo exportu**.

    ![Klikněte na Vytvořit úlohu importu a exportu.](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. V **základech**:

   * Vyberte **Importovat do Azure**.
   * Zadejte popisný název úlohy importu. Pomocí názvu můžete sledovat průběh úloh.
       * Název může obsahovat pouze malá písmena, čísla a pomlčky.
       * Název musí začínat písmenem a nesmí obsahovat mezery.
   * Vyberte předplatné.
   * Zadejte nebo vyberte skupinu prostředků.  

     ![Vytvořit úlohu importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. V **podrobnostech o úloze**:

   * Nahrajte soubory deníku jednotek, které jste získali během kroku přípravy jednotky. Pokud `waimportexport.exe version1` byl použit, nahrajte jeden soubor pro každou jednotku, kterou jste připravili. Pokud velikost souboru deníku přesáhne 2 `<Journal file name>_DriveInfo_<Drive serial ID>.xml` MB, můžete použít také vytvořený se souborem deníku.
   * Vyberte cílový účet úložiště, ve kterém budou data umístěna.
   * Umístění dropoff je automaticky naplněno na základě oblasti vybraného účtu úložiště.

   ![Vytvořit úlohu importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. V **zpáteční přepravní informace**:

   * V rozevíracím seznamu vyberte operátora. Pokud chcete použít jiného operátora než FedEx/DHL, zvolte stávající možnost z rozbalovací nabídky. Obraťte se na `adbops@microsoft.com` azure data box operations team na informace týkající se dopravce, který plánujete použít.
   * Zadejte platné číslo účtu dopravce, které jste vytvořili s tímto dopravcem. Společnost Microsoft používá tento účet k odeslání jednotek zpět k vám po dokončení importu úlohy. Pokud nemáte číslo účtu, vytvořte si účet operátora [FedEx](https://www.fedex.com/us/oadr/) nebo [DHL.](https://www.dhl.com/)
   * Uveďte úplné a platné kontaktní jméno, telefon, e-mail, adresu, město, zip, stát/provincii a zemi/oblast.

       > [!TIP]
       > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinový e-mail. Tím zajistíte, že budete dostávat oznámení i v případě, že správce odejde.

     ![Vytvořit úlohu importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. V **souhrnu**:

   * Zkontrolujte informace o úloze uvedené v souhrnu. Poznamenejte si název úlohy a dodací adresu datového centra Azure pro odesílání disků zpět do Azure. Tyto informace se používají později na přepravním štítku.
   * Chcete-li vytvořit úlohu importu, klepněte na tlačítko **OK.**

     ![Vytvořit úlohu importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Krok 3 (volitelné): Konfigurace klienta spravovaného klíče

Tento krok přeskočte a přejděte k dalšímu kroku, pokud chcete pomocí spravovaného klíče společnosti Microsoft chránit klíče nástroje BitLocker pro jednotky. Pokud chcete nakonfigurovat vlastní klíč pro ochranu klíče Nástroje bitlockeru, postupujte podle pokynů v [tématu Konfigurace klíčů spravovaných zákazníky pomocí azure key vaultu pro Azure Import and Export na webu Azure Portal](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Krok 4: Odeslání disků

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Krok 5: Aktualizace úlohy informacemi o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Krok 6: Ověření nahrávání dat do Azure

Sledování úlohy až do dokončení. Po dokončení úlohy ověřte, že se vaše data nahrála do Azure. Místní data odstraňte až po ověření, že nahrávání bylo úspěšné.

## <a name="next-steps"></a>Další kroky

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Zkontrolovat požadavky na import a export](storage-import-export-requirements.md)
