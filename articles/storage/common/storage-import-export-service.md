---
title: Používání Azure Import/Export pro přenos dat do a z Azure Storage | Microsoft Docs
description: Naučte se vytvořit import a export úloh na portálu Azure pro přenos dat do a z Azure Storage.
author: muralikk
manager: syadav
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/22/2018
ms.author: muralikk
ms.openlocfilehash: cc36fdde962ec44d679dc0e96f440b0437a84fa8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Použít službu Microsoft Azure Import/Export k přenosu dat do úložiště Azure.
V tomto článku jsme poskytují podrobné pokyny k používání služby Azure Import/Export bezpečně přenést velké objemy dat do úložiště objektů Blob v Azure a Azure Files jednotkami přenosů disku pro datové centrum Azure. Tato služba slouží také k přenosu dat ze služby Azure storage na jednotky pevného disku a dodávat místní servery. Buď do úložiště objektů Blob v Azure nebo Azure Files můžete importovat data z jednoho disku interní disků SATA. 

> [!IMPORTANT] 
> Tato služba přijímá pouze interní pevné disky SATA nebo SSD jenom. Žádné jiné zařízení je podporována. Neodesílat externí pevné disky, zařízení NAS, atd., jak se bude vrácen, jinak nebo pokud je to možné byla odstraněna.
>
>

Postupujte podle níže uvedených pokynů, pokud data na disku má být importován do úložiště Azure.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>Krok 1: Příprava jednotky nebo s nástroji WAImportExport a generovat deníku. soubor/s.

1.  Určete data, která mají být importován do úložiště Azure. Importem adresáře a samostatné soubory na místním serveru nebo sdílené síťové složce.
2.  V závislosti na celkové velikosti dat pořídit požadovaný počet 2,5 SSD nebo 2,5" nebo 3.5" SATA II nebo III jednotky pevného disku.
3.  Připojit přímo pomocí SATA pevných disků nebo s externí adaptéry USB k počítači s windows.
1.  Vytvořte jeden svazek NTFS na každý pevný disk a přiřadit písmeno jednotky svazku. Žádné přípojné body.
2.  Pokud chcete povolit šifrování na počítači systému windows, povolte šifrování schránku na svazku systému souborů NTFS. Postupujte podle pokynů na https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx.
3.  Úplně zkopírujte data do těchto šifrované jednoho systému souborů NTFS svazků na discích pomocí kopírování a vkládání nebo přetažení & rozevírací nebo Robocopy nebo takový nástroj.
7.  Stáhněte si WAImportExport V1 z https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.  Rozbalení souborů do waimportexportv1 složky výchozí. For example, C:\WaImportExportV1  
9.  Spustit jako správce a otevřete příkazový řádek nebo prostředí PowerShell a změňte adresář na rozbalené složce. Například cd C:\WaImportExportV1
10. Zkopírujte následující příkazový řádek do textového editoru a upravit ho vytvořit příkazového řádku:

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ /skipwrite 
    ```
    
    Tyto možnosti příkazového řádku jsou popsané v následující tabulce:

    |Možnost  |Popis  |
    |---------|---------|
    |/j:     |Název souboru deníku s příponou .jrn. Soubor deníku se generuje na jednotku. Doporučuje se použít jako název souboru deníku sériové číslo disku.         |
    |/Sk:     |Klíč účtu úložiště Azure.         |
    |/ t:     |Písmeno jednotky disk, který se má odeslat. Například jednotky `D`.         |
    |/BK:     |Klíč nástroje BitLocker pro jednotku. Jeho číselné heslo z výstupu ` manage-bde -protectors -get D: `      |
    |/srcdir:     |Následuje písmeno disku budou zaslány `:\`. Například, `D:\`.         |
    |/dstdir:     |Název cílové kontejneru ve službě Azure Storage         |
    |/skipwrite:     |Možnost, která určuje, že se žádná nová data potřeba kopírovat a existující data na disku je nutné připravit         |
1. Krok 10 opakujte pro každý disk, který musí být součástí.
2. Deník soubor s názvem zadaný pomocí parametru /j: se vytvoří pro každé spuštění příkazového řádku.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>Krok 2: Vytvoření úlohy importu na portálu Azure.

1. Přihlaste se k https://portal.azure.com/ a v části Další služby -> úložiště -> "úlohy importu a exportu" klikněte na tlačítko **úlohy importu a exportu vytvořit**.

2. V části základní informace vyberte "Importu do Azure", zadejte řetězec pro název úlohy, vyberte předplatné, zadejte nebo vyberte skupinu prostředků. Zadejte popisný název úlohy importu. Všimněte si, že, které zadáte název může obsahovat jenom malá písmena, číslice, pomlčky a podtržítka, musí začínat písmenem a nesmí obsahovat mezery. Můžete použít název, který jste se rozhodli sledovat vaše úlohy v době, kdy jsou v průběhu a po jejich dokončení.

3. V části Podrobnosti o úloze nahrajte soubory deníku jednotky, které jste získali během přípravy kroku jednotky. Pokud byl použit waimportexport.exe version1, budete muset nahrát jeden soubor pro každou jednotku, který jste připravili. Vyberte účet úložiště, který data bude naimportován do v části "Umístění importu" účet úložiště. Odkládací umístění se automaticky vyplní podle oblasti vybrat účet úložiště.
   
   ![Vytvoření úlohy importu – krok 3](./media/storage-import-export-service/import-job-03.png)
4. Na oplátku přesouvání části informace o zařadit vyberte z rozevíracího seznamu a zadejte číslo účtu platný operátora, kterou jste vytvořili pomocí tohoto operátora. Společnost Microsoft používá tento účet pro odeslání jednotky vám po dokončení importu úlohu. Zadejte úplný a platné jméno kontaktní osoby, telefon, e-mailu, adresu, města, zip, stavu nebo proviince a země nebo oblast.
   
5. V části Souhrn Azure DataCenter dodací adresy zajišťuje má být použit pro přesouvání disků, aby se řadič domény Azure. Ujistěte se, že název úlohy a úplnou adresu se zmiňují v popisku přesouvání. 

6. Klikněte na tlačítko OK na stránce Souhrn k dokončení vytvoření úlohy importu.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>Krok 3: Lodě disku/s do datového centra Azure přesouvání adresa zadaná v kroku 2.
FedEx, UPS nebo DHL lze použít pro odeslání balíčku, který má řadič domény Azure.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>Krok 4: Aktualizace úlohy vytvořené v krok 2 s sledování Číslo dodávky.
Po přesouvání disků, vraťte ke **importu a exportu** stránky na portálu Azure můžete aktualizovat číslo sledování pomocí následujícího postupu, a) přejděte a klikněte na import úlohy b) kliknutím na **aktualizace stavu úlohy a informace o sledování Jakmile jsou sice jednotky**. c) vyberte zaškrtávací políčko "Označit jako dodaný" d) zadejte číslo operátora a sledování.
Pokud toto číslo není aktualizován v rámci 2 týdny vytvoření úlohy, tato úloha vyprší. Průběh úlohy můžete sledovat na řídicím panelu portálu. V tématu každý stav úlohy v předchozí části znamená podle [zobrazení stavu vaše úlohy](#viewing-your-job-status).

## <a name="when-should-i-use-the-azure-importexport-service"></a>Kdy by se měla použít služba Azure Import/Export?
Zvažte použití služby Azure Import/Export při nahrávání nebo stahování dat přes síť je příliš pomalé nebo získání další šířku pásma sítě je cenovou konkurenceschopnost.

Tuto službu můžete použít ve scénářích, jako:

* Migrace dat do cloudu: rychle přesouvat velké objemy dat do Azure a nákladově efektivní.
* Distribuci obsahu: rychle posílat data do lokalit zákazníka.
* Zálohování: Trvat záloh vaše místní data ukládat do úložiště Azure.
* Obnovení dat: obnovení velké množství dat uložených v úložišti a nastavit doručení vaše místní umístění.

## <a name="prerequisites"></a>Požadavky
V této části jsme seznam požadovaných součástí pro tuto službu využívat. Přečtěte si je pečlivě před přesouvání jednotky.

### <a name="storage-account"></a>Účet úložiště
Musí mít stávající předplatné Azure a jeden nebo více účtů úložiště používat službu Import/Export. Azure Import/Export podporuje pouze classic, účty úložiště objektů Blob a účty úložiště v1 obecné účely. Každá úloha může použít k přenosu dat do nebo z jenom jeden účet úložiště. Jinými slovy úlohu jeden importu a exportu nelze rozmístěny napříč více účtů úložiště. Informace o vytvoření nového účtu úložiště najdete v tématu [postup vytvoření účtu úložiště](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Služba Azure Import Export nepodporuje účty úložiště kde [koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md) funkce povolena. 
> 
> 

### <a name="data-types"></a>Typy dat
Služba Azure Import/Export můžete použít ke zkopírování dat do **bloku** objekty BLOB, **stránky** objekty BLOB, nebo **soubory**. Naopak můžete pouze exportovat **bloku** objekty BLOB, **stránky** objektů BLOB nebo **připojení** objekty BLOB ze služby Azure storage používání této služby. Služba podporuje pouze import souborů Azure do úložiště Azure. Export souborů Azure není aktuálně podporován.

### <a name="job"></a>Úloha
Chcete-li zahájit proces pro import nebo export z úložiště, nejprve vytvořit úlohu. Úloha může být úloha importu nebo úlohy exportu:

* Pokud chcete k přenosu dat do účtu úložiště Azure máte místní, vytvoření úlohy importu.
* Vytvoření úlohy exportu, pokud chcete k přenosu dat, které jsou aktuálně uloženy ve vašem účtu úložiště pro pevné disky, které jsou součástí společnosti Microsoft. Když vytvoříte úlohu, můžete upozornit službu Import/Export, že jste se distribuovat jeden nebo více pevných disků pro datové centrum Azure.

* Pro úlohy importu bude přesouvání pevné disky obsahující data.
* Pro úlohy exportu bude přesouvání prázdný pevné disky.
* Můžete zaslat až 10 pevných disků na úlohu.

Můžete vytvořit importu nebo exportu úlohy pomocí portálu Azure nebo [REST API služby Azure Storage importu a exportu](/rest/api/storageimportexport).

> [!Note]
> Rozhraní API RDFE již nebude podporována 28 února 2018 a vyšší. Chcete-li pokračovat v používání služby, migrujte [ARM importu a exportu REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json). 

### <a name="waimportexport-tool"></a>Nástroj WAImportExport
Prvním krokem při vytváření **importovat** úloha je k přípravě vaše jednotky, které bude dodáno pro import. Příprava jednotky, je třeba připojit k místní server a spustit nástroj WAImportExport na místním serveru. Tento nástroj WAImportExport usnadňuje kopírování dat na jednotku, šifrování dat na jednotce s nástrojem BitLocker a generování souborů deníku jednotky.

Soubory deníku ukládat základní informace o úloze a jednotky, jako jsou jednotky sériové číslo a název účtu úložiště. Tento soubor deníku není uložená na disku. Používá se při vytvoření úlohy importu. Podrobné informace o vytvoření úlohy najdete dál v tomto článku.

Nástroj WAImportExport je jenom kompatibilní s operačním systémem Windows 64-bit. Najdete v článku [operačního systému](#operating-system) části pro konkrétní verze operačního systému podporován.

Stáhněte si nejnovější verzi [WAImportExport nástroj](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Další podrobnosti o použití nástroje WAImportExport najdete v tématu [pomocí nástroje WAImportExport](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Předchozí verze:** můžete [stáhnout WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) verze nástroje a odkazovat na [WAImportExpot V1 použití průvodce](storage-import-export-tool-how-to-v1.md). Verze WAImportExpot V1 nástroje poskytují podporu pro **Příprava disky, když je už předem zapisovat data na disk**. Pokud je k dispozici pouze klíč SAS klíč, budete muset použít nástroj WAImportExpot V1.

>

### <a name="hard-disk-drives"></a>Jednotky pevného disku
Pouze 2,5 SSD nebo 2,5" nebo 3.5" SATA II nebo interní HDD III jsou podporovány pro použití se službou importu a exportu. Úlohu jeden importu a exportu může mít maximálně 10 pevný disk nebo disky SSD a každé jednotlivé HDD/SSD může mít libovolnou velikost. Velký počet jednotek možné rozdělit do více úloh a neexistuje žádná omezení na počet úloh, které lze vytvořit. 

Pro import úlohy jsou zpracovávány pouze první datový svazek na disku. Datový svazek musí být formátován pomocí systému souborů NTFS.

> [!IMPORTANT]
> Tato služba nepodporuje externí jednotky pevného disku, které jsou předdefinované adaptérem USB. Navíc nelze použít uvnitř malá a velká písmena externí pevný disk na disk; Neodesílat externí pevné disky.
> 
> 

Tady je seznam externích adaptéry USB použít ke zkopírování dat do interní pevné disky. Anker 68UPSATAA - 02BU Anker 68UPSHHDS BU Startech SATADOCK22UE Orico 6628SUS3-C-černá (6628 řada) Thermaltake BlacX odkládacího horká SATA externí pevné jednotky ukotvení stanice (USB 2.0 & eSATA)

### <a name="encryption"></a>Šifrování
Data na disku musí být šifrované pomocí nástroj BitLocker Drive Encryption. Toto šifrování chrání vaše data, i když je při přenosu.

Pro import úlohy existují dva způsoby, jak provést šifrování. První způsob je zadejte možnost při použití souboru CSV datové sady při spuštění nástroje WAImportExport během přípravy na jednotku. Druhý způsob je povolíte šifrování nástrojem BitLocker na jednotce ručně a zadejte šifrovací klíč v driveset sdíleného svazku clusteru při spuštění WAImportExport nástroj příkazového řádku během přípravy na jednotku.

Pro úlohy exportu po zkopírování dat na discích, bude služba šifrování jednotky pomocí nástroje BitLocker před přesouvání zpět do. Šifrovací klíč je poskytnutá prostřednictvím portálu Azure.  

### <a name="operating-system"></a>Operační systém
Příprava pevný disk pomocí nástroje WAImportExport před přesouvání jednotky do Azure můžete použít jednu z následujících 64bitových operačních systémů:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Všechny tyto operační systémy podporují nástroj BitLocker Drive Encryption.

### <a name="locations"></a>Umístění
Služba Azure Import/Export podporuje kopírování dat do a ze všech účtů úložiště veřejný Azure. Můžete zaslat jednotky pevného disku na jeden z uvedených umístění. Pokud váš účet úložiště je do veřejného umístění Azure, který není zde určený přesouvání alternativní umístění bude třeba zadat při vytváření úlohy pomocí portálu Azure nebo REST API pro Import nebo Export.

Podporované přenosů umístění:

* Východ USA
* Západní USA
* Východní USA 2
* Západní USA 2
* Střed USA
* Střed USA – sever
* Střed USA – jih
* Západní střed USA
* Severní Evropa
* Západní Evropa
* Východní Asie
* Jihovýchodní Asie
* Austrálie – východ
* Austrálie – jihovýchod
* Japonsko – západ
* Japonsko – východ
* Střed Indie
* Indie – jih
* Indie – západ
* Střední Kanada
* Východní Kanada
* Brazílie – jih
* Korea – střed
* USA (Gov) – Virginia
* USA (Gov) – Iowa
* US DoD – východ
* US DoD – střed
* Čína – východ
* Čína – sever
* Spojené království – jih
* Německo – střed
* Německo – severovýchod

### <a name="shipping"></a>Expedice
**Přesouvání jednotky k datovému centru:**

Při vytváření úlohu import nebo export, bude třeba zadat adresu příjemce jednoho z podporovaných umístění pro odeslání jednotky. Je zadaná adresa přenosů závisí na umístění účtu úložiště, ale nemusí být stejné jako vaše umístění účtu úložiště.

FedEx, UPS nebo DHL lze použít pro odeslání na adresu přesouvání jednotky.

**Přesouvání jednotky v datovém centru:**

Při vytváření úlohu import nebo export, musí se zadat zpáteční adresu pro společnost Microsoft k použití při přesouvání jednotky zpět po dokončení úlohy. Ujistěte se, že zadáte platná zpáteční adresa. aby se zabránilo zpoždění při zpracování.

Zařadit by měl mít odpovídající sledování, aby byla zachována o jeho postupném předávání. Je nutné zadat platný FedEx, UPS, nebo číslo účtu DHL poskytovatel použije společností Microsoft pro přesouvání jednotky zpět. FedEx, UPS nebo DHL číslo účtu je vyžadována pro přesouvání jednotky zpět z USA a Evropě umístění. DHL účet je požadováno pro přesouvání jednotky zpět z Asii a Austrálie umístění. Pokud jeden nemáte, můžete vytvořit [FedEx](http://www.fedex.com/us/oadr/) (pro USA a Evropě) nebo [DHL](http://www.dhl.com/) poskytovatel účtu (Asii a Austrálie). Pokud již máte účet číslo operátora, ověřte, zda je platný.

V přesouvání vlastních balíčků, je třeba postupovat podle podmínek na [podmínky služby Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Všimněte si, že fyzická média, která jsou přesouvání muset křížové mezinárodní hranice. Jste zodpovědní za zajištění, vaše fyzická média a data jsou importovat a exportovat v souladu s platné zákony. Před jejich odesláním fyzických médií, zkontrolujte s vaší poradci ověřit, jestli se médiu a dat můžete souladu s právem odeslaná do identifikovaného datového centra. To pomůže zajistit, aby obdržel Microsoft včas. Například všechny balíček, který protne mezinárodní hranice musí obchodní faktury, chcete-li být dodávány spolu s balíčkem (s výjimkou Pokud překračování hranic v rámci Evropské unie). Může vytiskněte vyplněný kopie komerční faktury z webu poskytovatel. Příklad komerční faktury jsou [DHL komerční faktury](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) a [FedEx komerční faktury](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Ujistěte se, že Microsoft nebyl byly označeny jako exportu.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Jak funguje služba Azure Import/Export?
Přenos dat mezi místními servery a vytvořením úlohy a přesouvání jednotky pevného disku pro datové centrum Azure pomocí služby Azure Import/Export úložiště Azure. Každý pevný disk, dodávané se přidružit k jedné úloze. Každá úloha je přidružený k účtu jedno úložiště. Zkontrolujte [část předpoklady](#pre-requisites) pečlivě a zjistěte, jaké jsou specifikace této služby, například podporované datové typy, typy, umístění a disku přesouvání.

V této části jsou popsané kroky vysoké úrovně musíte import a export úloh. Dále v [Quick Start článku](#quick-start), poskytuje podrobné pokyny k vytvoření importu a exportu úlohy.

### <a name="inside-an-import-job"></a>Uvnitř úlohy importu
Na vysoké úrovni úloha importu zahrnuje následující kroky:

* Určete data, která mají být importována a počet jednotek, které potřebujete.
* Určete cílové umístění objektu blob nebo soubor pro vaše data v úložišti Azure.
* Pomocí nástroje WAImportExport si zkopírujte svá data na jeden nebo více pevných disků a je šifrování pomocí nástroje BitLocker.
* Vytvoření úlohy importu v účtu úložiště cíl pomocí portálu Azure nebo REST API pro Import nebo Export. Pokud používáte portál Azure, nahrajte soubory deníku jednotky.
* Zadejte zpáteční adresu a číslo účtu poskytovatel má být použit pro přesouvání jednotky pro vás.
* Dodávat pevných disků o přenosů adresa zadaná při vytvoření úlohy.
* Aktualizujte doručení sledování Číslo v podrobnostech úlohy importu a odeslání úlohy importu.
* Disky jsou přijata a zpracovat datového centra Azure.
* Jednotky jsou dodávané pomocí účtu poskytovatel návratové adresy uvedené v úloze importu.
  
    ![Obrázek toku 1:Import úlohy](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Uvnitř úlohy exportu
> [!IMPORTANT]
> Služba podporují pouze export objektů BLOB Azure a nepodporuje export souborů Azure...
> 
>

Na vysoké úrovni úlohy exportu zahrnuje následující kroky:

* Určete data, která mají být exportovány a počet jednotek, které potřebujete.
* Určete zdroj objektů BLOB nebo kontejneru cesty vašich dat v úložišti objektů Blob.
* Vytvoření úlohy exportu ve vašem účtu úložiště zdroje pomocí portálu Azure nebo REST API pro Import nebo Export.
* Zadejte zdroj objektů BLOB nebo kontejneru cest vaše data v úloha exportu.
* Zadejte zpáteční adresu a číslo účtu poskytovatel má být použit pro přesouvání jednotky pro vás.
* Dodávat pevných disků o přenosů adresa zadaná při vytvoření úlohy.
* Aktualizujte doručení číslo v podrobnostech úlohy exportu sledování a odeslání úlohy exportu.
* Jednotky jsou přijme a zpracuje v datového centra Azure.
* Jednotky jsou šifrované pomocí nástroje BitLocker; klíče jsou k dispozici prostřednictvím portálu Azure.  
* Jednotky jsou dodávané pomocí účtu poskytovatel návratové adresy uvedené v úloze importu.
  
    ![Obrázek toku 2:Export úlohy](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Zobrazení stavu úlohy a jednotky
Můžete sledovat stav import nebo export úloh z portálu Azure. Klikněte **importu a exportu** kartě. Na stránce se zobrazí seznam vašich úloh.

![Zobrazení stavu úlohy](./media/storage-import-export-service/jobstate.png)

Najdete v jednom z následujících stavů úlohy v závislosti na tom, kde je vaše jednotka v procesu.

| Stav úlohy | Popis |
|:--- |:--- |
| Vytváření | Po vytvoření úlohy, je její stav nastavit na vytváření. Když úloha je ve stavu vytvoření, službu Import/Export předpokládá, že jednotky nebyly byla odeslaná do datového centra. Úlohy mohou zůstat ve stavu vytvoření až dvou týdnů, po které se automaticky odstraní službou. |
| Expedice | Po dodáte vašeho balíčku, by měl aktualizovat informace o sledování na portálu Azure.  Tato úloha zapnout do "Přesouvání". Úloha zůstane ve stavu přesouvání dobu až dvou týdnů. 
| Přijato | Po přijetí všech jednotkách v datovém centru, nastaví se na přijaté stav úlohy. |
| Probíhá přesun | Alespoň jedna jednotka zahájení zpracování, bude stav úlohy na přenos nastavovat. Najdete v části stavy jednotky pod podrobné informace. |
| Balení | Po dokončení zpracování všech jednotkách, úlohy budou umístěny ve stavu balení dokud jednotky jsou sice vám. |
| Dokončené | Po všechny jednotky byly dodány zpět na zákazníka, pokud úloha byla dokončena bez chyb, bude úloha nastavit stav dokončeno. Úloha se automaticky odstraní po 90 dnech ve stavu dokončeno. |
| Uzavřeno | Po všechny jednotky byly dodány zpět na zákazníka, pokud zde nejsou žádné chyby během zpracování úlohy, bude úloha nastavit na zavřeném stavu. Úlohy budou automaticky odstraněny po 90 dnech v uzavřeném stavu. |

Následující tabulka popisuje životní cyklus jednotlivé jednotky jako přechází prostřednictvím úlohu import nebo export. Aktuální stav každé jednotky, v rámci úlohy je nyní viditelné z portálu Azure.
Následující tabulka popisuje všechny stavy, které může předávat každé jednotky, v rámci úlohy.

| Stav disku | Popis |
|:--- |:--- |
| Zadaný | Pro úlohu importu při vytvoření úlohy z portálu Azure počáteční stav pro jednotku je zadaná stavu. Pro úlohy exportu vzhledem k tomu, že není zadána žádná jednotka při vytvoření úlohy, stav počáteční jednotky je stav přijaté. |
| Přijato | Jednotka přechody stavu přijaté při importu a exportu služby operátor má zpracování jednotek, které byly přijaty z společnosti přesouvání úlohy importu. Stav počáteční jednotky pro úlohy exportu, je stav přijaté. |
| NeverReceived | Jednotka se přesune do stavu NeverReceived při přijetí balíčku pro úlohu, ale balíček neobsahuje jednotku. Jednotku také můžete přesunout do tohoto stavu, pokud to bylo dva týdny, protože služba přijala přesouvání informace, ale balíček nebyl ještě přijaty v datovém centru. |
| Probíhá přesun | Na jednotku se přesune do stavu přenos zahájení službu k přenosu dat z jednotky do služby Windows Azure Storage. |
| Dokončené | Jednotku přesune do stav dokončeno, když služba má úspěšně přenesla všechna data bez chyb.
| CompletedMoreInfo | Jednotku přesune do stavu CompletedMoreInfo, když služba zjistila některé problémy při kopírování dat z nebo na jednotku. Informace může obsahovat chyby, upozornění a informativní zprávy o přepsání objektů BLOB.
| ShippedBack | Jednotka přesune do stavu ShippedBack má byla zakoupení z center zálohování dat na návratovou adresu. |

Tuto bitovou kopii z portálu Azure zobrazuje stav disku úlohu příklad:

![Zobrazení stavu jednotky](./media/storage-import-export-service/drivestate.png)

Následující tabulka popisuje stavy selhání jednotky a akcí provedených pro každý stav.

| Stav disku | Událost | Řešení / další krok |
|:--- |:--- |:--- |
| NeverReceived | Jednotka, která je označena jako NeverReceived (protože nebyla přijata jako součást úlohy dodávky) dorazí v jiné dodávky. | Provozní tým přesune do stavu přijaté jednotku. |
| neuvedeno | Jednotku, která není součástí všechny úlohy dorazí v datovém centru jako součást jiná úloha. | Jednotka budou označeny jako další jednotky a obnoví se zákazník při dokončení úlohy spojené s původní balíčku. |

### <a name="time-to-process-job"></a>Čas do procesu úlohy
Dobu potřebnou ke zpracování úlohu importu a exportu se liší v závislosti na počtu faktorů, jako jsou například dobu dodání, zatížení v datovém centru, typ úlohy a velikosti dat kopírovány a počet disků v rámci úlohy. Import a Export služba nemá SLA ale službu snaží dokončení kopírování v 7 až 10 dnů po disky jsou přijaty. Kromě stavu odeslány na portálu Azure rozhraní REST API umožňuje sledovat průběh úlohy. Parametr procenta dokončení v seznamu úloh operaci volání rozhraní API poskytuje průběh kopírování procento.

### <a name="pricing"></a>Ceny
**Jednotka poplatek za zpracování**

Je poplatek za zpracování jednotky pro každou jednotku zpracovat jako součást import nebo export úlohy. Zobrazit podrobnosti na [Azure Import/Export ceny](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Přesouvání náklady**

Jestliže doplníte jednotky do Azure, platíte náklady na přesouvání poskytovatel přesouvání. Když se Microsoft vrátí jednotky pro vás, náklady na přesouvání je zodpovědné za poskytovatel účet, který jste zadali při vytvoření úlohy.

**Cena za transakci**

Při importu dat do Azure Storage neexistují žádné cena za transakci kromě standardního úložiště cena za transakci. Standardní výstupní poplatky se dají použít při exportu dat z úložiště objektů Blob. Další informace o cena za transakci, v [přenos dat se ceny.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>Jak importovat data do Azure File Storage pomocí interní SSD a pevných disků SATA?
Postupujte podle níže uvedených pokynů, pokud má být importován do Azure File Storage data na disku.
Prvním krokem při importu dat pomocí služby Azure Import/Export je příprava jednotky pomocí nástroje WAImportExport. Postupujte podle následujících kroků a příprava jednotky.

1. Určete data, která mají být importován do Azure File Storage. To může být adresářů a samostatné soubory na místním serveru nebo sdílené síťové složce.  
2. Určete počet jednotek, které budete potřebovat v závislosti na celkovou velikost data. Pořídit požadovaný počet 2,5 SSD nebo 2,5" nebo 3.5" SATA II nebo III jednotky pevného disku.
4. Určí, adresáře nebo samostatné soubory, které se zkopírují na každý pevný disk.
5. Vytvoření souborů CSV pro datovou sadu a driveset.
    
  Níže je příklad souboru CSV datovou sadu ukázka pro import dat jako soubory Azure:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   V předchozím příkladu 100M_1.csv.txt se zkopírují do kořenového adresáře "sdílení souborů". Pokud "Sdílení souborů" neexistuje, bude vytvořen. Všechny soubory a složky v části 50M_original bude rekurzivně zkopírován do sdílení souborů. Struktura složek budou zachována.

    Další informace o [soubor CSV datovou sadu se připravuje](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **Soubor Driveset CSV**

    Hodnota příznaku driveset je soubor CSV, který obsahuje seznam disků, na které jsou namapované písmena jednotek, aby nástroj, který správně vyberte seznam disků, abyste byli připraveni. 

    Dole je příklad driveset souboru CSV:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    Ve výše uvedeném příkladu se předpokládá, že jsou připojené dva disky a základní svazků systému souborů NTFS s G:\ písmeno svazku a H:\ byly vytvořeny. Nástroj formátu a šifrování disku, který je hostitelem H:\ a nebude formátu nebo šifrování disku, který je hostitelem svazku G:\.

    Další informace o [Příprava souboru CSV driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Použití [WAImportExport nástroj](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) ke kopírování dat na jeden nebo více pevných disků.
7.  Můžete zadat "Šifrovat" na pole šifrování v drivset CSV povolíte šifrování nástrojem BitLocker na jednotce pevného disku. Alternativně můžete také povolíte šifrování nástrojem BitLocker na jednotce pevného disku ručně a zadejte "AlreadyEncrypted" a zadejte klíče v driveset sdíleného svazku clusteru při spuštění nástroje.

8. Neprovádějte žádné změny dat na jednotky pevného disku nebo souboru deníku po dokončení Příprava disku.

> [!IMPORTANT]
> Každý jednotku pevného disku, které připravíte způsobí souboru deníku. Při vytváření úlohy importu pomocí portálu Azure, musíte nahrát všechny soubory deníku jednotek, které jsou součástí této úlohy importu. Disky bez deníku, které soubory nebudou zpracována.
> 
>

Níže jsou příklady pro přípravu na pevný disk pomocí WAImportExport nástroje a příkazy.

Příkaz PrepImport WAImportExport nástroj pro první relaci kopírování ke kopírování adresářů a souborů s novou relací kopie:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Import Příklad 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Za účelem **přidat další jednotky**, jeden vytvořit nový soubor driveset a spusťte příkaz jak je uvedeno níže. Pro následné kopírování relace jiné diskových jednotek než je zadáno v souboru .csv InitialDriveset zadejte nový soubor CSV driveset a zadejte jako hodnotu parametru "AdditionalDriveSet". Použití **stejný soubor deníku** název a zadejte **nové ID relace**. Formát souboru AdditionalDriveset CSV je stejný jako formát InitialDriveSet.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Import příklad 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Chcete-li přidat další data do stejné driveset, WAImportExport nástroj příkaz PrepImport lze volat pro následné kopírování relací pro kopírování další soubory nebo adresáře: pro následné kopírování relace na stejné jednotky pevného disku zadaná v souboru .csv InitialDriveset, zadejte **stejný soubor deníku** název a zadejte **nové ID relace**; je potřeba zadat klíč účtu úložiště.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Import příklad 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Zobrazte další podrobnosti o použití nástroje WAImportExport v [Příprava pevné disky pro import](storage-import-export-tool-preparing-hard-drives-import.md).

Navíc vztahují [ukázkový pracovní postup přípravy pevné disky na úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) podrobnější pokyny krok za krokem.  



## <a name="create-an-export-job"></a>Vytvoření úlohy exportu
Vytvoření úlohy exportu službu Import/Export oznámit, že je budete distribuovat jedné nebo několika prázdný jednotek do datového centra, aby data můžete exportovat z vašeho účtu úložiště do jednotky a jednotky a dodání.

### <a name="prepare-your-drives"></a>Příprava jednotky
Příprava jednotky pro úlohy exportu doporučujeme následující předběžné kontroly:

1. Zkontrolujte počet disků požadované pomocí nástroje WAImportExport PreviewExport příkazu. Další informace najdete v tématu [náhled jednotka využití pro úlohu exportovat](https://msdn.microsoft.com/library/azure/dn722414.aspx). Umožňuje zobrazit náhled využití disku pro objekty BLOB, které jste vybrali, na základě velikosti jednotky, které chcete použít.
2. Zkontrolujte, jestli je můžete pro čtení a zápis na pevný disk, který má být dodán pro úlohu export.

### <a name="create-the-export-job"></a>Vytvoření úlohy exportu
1. Chcete-li vytvořit úlohy exportu, přejděte na další služby -> úložiště -> "úlohy importu a exportu" na portálu Azure. Klikněte na tlačítko **úlohy importu a exportu vytvořit**.
2. V kroku 1 základy vyberte "Export z Azure", zadejte řetězec pro název úlohy, vyberte předplatné, zadejte nebo vyberte skupinu prostředků. Zadejte popisný název úlohy importu. Všimněte si, že, které zadáte název může obsahovat jenom malá písmena, číslice, pomlčky a podtržítka, musí začínat písmenem a nesmí obsahovat mezery. Použijete název, který jste se rozhodli sledovat vaše úlohy v době, kdy jsou v průběhu a po jejich dokončení. Zadejte kontaktní informace osoby odpovědné za tuto úlohu export. 

3. V kroku 2 úlohy podrobnosti vyberte účet úložiště, který se data se exportují z v části účet úložiště. Odkládací umístění se automaticky vyplní podle oblasti vybrat účet úložiště. Určete, jaká data objektů blob, které chcete exportovat z vašeho účtu úložiště do prázdné jednotku nebo jednotky. Můžete exportovat všechny data objektů blob v účtu úložiště, nebo můžete určit, které objekty BLOB nebo nastaví objektů blob pro export.
   
   Pokud chcete zadat objekt blob pro export, použijte **rovno** selektor a zadejte relativní cestu do objektu blob, počínaje název kontejneru. Použití *$root* k určení Kořenový kontejner.
   
   K určení všech objektů BLOB od s předponou, použijte **začíná** selektor a zadejte předponu, počínaje lomítkem '/'. Předpona, která může být předponu název kontejneru, název dokončení kontejneru nebo název dokončení kontejneru, za nímž následuje předponu názvu objektu blob.
   
   Následující tabulka uvádí příklady cesty platný objekt blob:
   
   | Selektor | Cesta k objektu BLOB | Popis |
   | --- | --- | --- |
   | Začíná |/ |Exportuje všech objektů BLOB v účtu úložiště |
   | Začíná |/$root / |Exportuje všech objektů BLOB v kontejneru kořenové |
   | Začíná |/Book |Exportuje všech objektů BLOB v kontejneru, který začíná předponu **adresáře** |
   | Začíná |/Music/ |Exportuje všech objektů BLOB v kontejneru **Hudba** |
   | Začíná |/ Hudba/láska |Exportuje všech objektů BLOB v kontejneru **Hudba** které začínají předponou **rádi** |
   | Rovno |$root/logo.bmp |Export objektu blob **logo.bmp** v kořenovém kontejneru |
   | Rovno |videos/story.mp4 |Export objektu blob **story.mp4** v kontejneru **videa** |
   
   Cesty objektů blob v platné formáty, aby nedocházelo k chybám při zpracování, je nutné zadat, jak je vidět na tomto snímku obrazovky.
   
   ![Vytvoření úlohy exportu – krok 3](./media/storage-import-export-service/export-job-03.png)

4. V kroku 3 vrátit přesouvání informace vyberte z rozevíracího seznamu zařadit a zadejte číslo účtu platný poskytovatel, kterou jste vytvořili pomocí tohoto poskytovatel. Microsoft použije tento účet pro odeslání jednotky vám po dokončení importu úlohu. Zadejte úplný a platné jméno kontaktní osoby, telefon, e-mailu, adresu, města, zip, stavu nebo proviince a země nebo oblast.
   
 5. Na stránce Souhrn přenosů adres Azure DataCenter zajišťuje má být použit pro přesouvání disků, aby se řadič domény Azure. Ujistěte se, že název úlohy a úplnou adresu se zmiňují v popisku přesouvání. 

6. Klikněte na tlačítko OK na stránce Souhrn k dokončení vytvoření úlohy importu

7. Po přesouvání disků, vraťte ke **importu a exportu** stránky na portálu Azure a) přejděte a klikněte na úlohu importu b) klikněte na **aktualizace stavu úlohy a informace o sledování, jakmile jsou sice jednotky**. 
     c) vyberte zaškrtávací políčko "Označit jako dodaný" d) zadejte číslo operátora a sledování.
    
   Pokud toto číslo není aktualizován v rámci 2 týdny vytvoření úlohy, tato úloha vyprší.
   
8. Průběh úlohy můžete sledovat na řídicím panelu portálu. V tématu každý stav úlohy v předchozí části znamená podle [zobrazení stavu vaše úlohy](#viewing-your-job-status).

   > [!NOTE]
   > Pokud objekt blob export se používá v době kopírování na pevném disku, bude služba Azure Import/Export pořízení snímku objektu blob a zkopírujte snímku.
   > 
   > 
 
9. Jakmile se zobrazí na discích s exportovaná data, můžete zobrazit a zkopírujte tyto klíče nástroje BitLocker vygenerované službou pro jednotce. Přejděte do exportu úlohy na portálu Azure a klikněte na kartu importu a exportu. Vyberte úlohu export ze seznamu a klikněte na možnost klíče Bitlockeru. Klíče Bitlockeru vypadat takto:
   
   ![Zobrazit klíče nástroje BitLocker pro úlohu export](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Přejděte prostřednictvím níže v části Nejčastější dotazy, jak vysvětluje nejběžnější otázky, které zákazníci setkávají při používání této služby.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Můžete zkopírovat pomocí služby Azure Import/Export úložiště Azure File?**

Ano, podporuje službu Azure Import/Export importovat do Azure File Storage. Export souborů Azure v tuto chvíli nepodporuje.

**Je dostupná pro předplatná CSP služba Azure Import/Export?**

Služba Azure Import/Export podporuje odběry CSP.

**Můžete přeskočit krok přípravy jednotky pro úlohy importu nebo můžete připravit na jednotku bez kopírování?**

Všechny jednotky, který chcete použít pro odeslání pro import dat musí být připraveny pomocí nástroje Azure WAImportExport. Musíte použít nástroj WAImportExport ke zkopírování dat na disk.

**Je potřeba provést jakékoli přípravu disku při vytváření úlohy exportu?**

Doporučuje se žádné, ale některé předběžné kontroly. Zkontrolujte počet disků požadované pomocí nástroje WAImportExport PreviewExport příkazu. Další informace najdete v tématu [náhled jednotka využití pro úlohu exportovat](https://msdn.microsoft.com/library/azure/dn722414.aspx). Umožňuje zobrazit náhled využití disku pro objekty BLOB, které jste vybrali, na základě velikosti jednotky, které chcete použít. Také zkontrolujte, zda lze číst z a zapisovat na pevný disk, který má být dodán pro úlohu export.

**Co se stane, když I omylem poslat pevný disk, který není v souladu s požadavky na podporované?**

Datového centra Azure vrátí na jednotku, která není v souladu s požadavky na podporované pro vás. Pokud jenom některé jednotky v balíčku splnit požadavky na podporu, tyto jednotky se zpracují a jednotky, které nesplňují požadavky na bude vrácen vám.

**Můžete zrušit má úloha?**

Úlohy můžete zrušit, pokud je jeho stav vytváření nebo přesouvání.

**Jak dlouho na portálu Azure můžete zobrazit stav dokončené úlohy?**

Můžete zobrazit stav pro dokončené úlohy po dobu 90 dnů. Dokončené úlohy budou odstraněny po 90 dnech.

**Pokud chcete importovat nebo exportovat víc než 10 jednotky, co mám dělat?**

Jeden import nebo export úloha může odkazovat jenom 10 jednotky v rámci jedné úlohy pro službu Import/Export. Pokud chcete pro odeslání více než 10 disků, můžete vytvořit více úloh. Jednotky, které jsou přidruženy k této úloze stejné musí dodávané společně ve stejném balíčku.
Společnost Microsoft nabízí pokyny a pomoc při kapacity dat zahrnuje více disku úlohy importu. Obraťte se na bulkimport@microsoft.com Další informace

**Službu formátu jednotky před vrácením je?**

Ne. Všechny jednotky jsou šifrované pomocí Bitlockeru.

**Můžete zakoupit jednotky pro úlohy importu a exportu od společnosti Microsoft?**

Ne. Musíte dodávat vlastní jednotky pro obě import a export úloh.

** Jak může přistupovat k datům, importovaných pomocí této služby **

Data v rámci účtu úložiště Azure se dají zpřístupnit přes portál Azure nebo pomocí samostatný nástroj nazývá Průzkumník úložišť. https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**Po dokončení úlohy importu, co bude Moje data vypadat v účtu storage? Moje hierarchie adresářů se zachovají?**

Při přípravě na pevný disk pro úlohy importu, je zadána cílového pole DstBlobPathOrPrefix v datové sadě sdíleného svazku clusteru. Toto je cílový kontejner v účtu úložiště, ke kterému se zkopíruje data z pevného disku. V tomto kontejneru cílové virtuální adresáře jsou vytvořeny pro složky z pevného disku a objekty BLOB jsou vytvořené pro soubory. 

**Pokud má jednotka souborů, které již existují v svůj účet úložiště, služba přepíše existující objekty BLOB či soubory v svůj účet úložiště?**

Při přípravě na jednotku, můžete určit, zda by dojít k přepsání souborů cílové nebo ignorováno pomocí pole v souboru CSV datovou sadu názvem dispozice: < přejmenovat | přepsat ne | přepsat >. Ve výchozím nastavení služba bude přejmenovat nové soubory a nikoli přepsat existující objekty BLOB nebo soubory.

**Je nástroj WAImportExport kompatibilní s 32bitové operační systémy?**
Ne. Nástroj WAImportExport je jenom kompatibilní s operačními systémy Windows 64-bit. Naleznete v části operační systémy v [předpoklady](#pre-requisites) pro úplný seznam podporovaných verzí operačního systému.

**By měla obsahovat jakoukoli jinou hodnotu než jednotku pevného disku v mé balíčku?**

Prosím dodávat pouze pevné disky. Nezahrnujte věci, jako je napájecích kabelů napájení nebo kabely USB.

**Je nutné dodávat Moje jednotky pomocí FedEx nebo DHL?**

Můžete zaslat jednotky do datového centra pomocí žádné známé poskytovatel jako FedEx DHL, UPS nebo nám poštovní služby. Pro přesouvání jednotky vám v datovém centru, je však nutné zadat číslo účtu FedEx v USA a EU nebo DHL číslo účtu v Asii a Austrálie oblastech.

**Existují nějaká omezení s přesouvání mezinárodní úrovni svou jednotku?**

Upozorňujeme, že fyzická média, která jsou přesouvání muset křížové mezinárodní hranice. Jste zodpovědní za zajištění, vaše fyzická média a data jsou importovat a exportovat v souladu s platné zákony. Před jejich odesláním fyzických médií, zkontrolujte s vaší poradci ověřit, jestli se médiu a dat můžete souladu s právem odeslaná do identifikovaného datového centra. To vám pomůže zajistit, aby obdržel Microsoft včas.

**Při vytváření úlohy, adresy příjemce je umístění, které se liší od umístění účtu úložiště. Co bych měl/a dělat?**

Některé umístění účtu úložiště jsou namapované na alternativní přesouvání umístění. Dříve dostupná přenosů umístění můžete také dočasně mapovat do alternativního umístění. Vždy zkontrolujte přenosů adresa zadaná při vytváření úlohy před přesouvání jednotky.

**Při přesouvání svou jednotku, poskytovatel požádá o data center adresa a telefonní číslo kontaktu. Co by měl poskytovat?**

Řadič domény a telefonní číslo adresy se poskytuje jako součást úlohy vytvoření.

**Můžete použít službu Azure Import/Export pro kopírování PST poštovní schránky a dat služby SharePoint k O365?**

Naleznete [Import PST souborů nebo dat služby SharePoint do služeb Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Můžete použít službu Azure Import/Export pro kopírování Moje zálohování offline ke službě Azure Backup?**

Naleznete [pracovní postup Offline zálohování v Azure Backup](../../backup/backup-azure-backup-import-export.md).

**Jaký je maximální počet HDD pro v jedné dodávky?**

Může být libovolný počet pevných disků v jedné dodávky a pokud disky patří do více úloh se doporučuje) disky s názvem bez přípony s odpovídající název úlohy. b) aktualizovat úlohy s číslem sledování na konci -1,-2 atd.
  
**Jaký je maximální objekt Blob bloku a velikost objektu Blob stránky podporovány disku importu a exportu?**

Objekt Blob bloku maximální velikost je přibližně 4.768TB nebo 5 000 000 MB.
Objekt Blob stránky maximální velikost je 1TB.

**Podporuje disku importu a exportu šifrování AES 256?**

Služba Azure Import/Export ve výchozím nastavení zašifruje pomocí nástroje bitlocker šifrování AES 128, ale to je možné zvýšit na AES 256 ručně šifrování nástrojem bitlocker před data budou zkopírována. 

Pokud používáte [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), zde je ukázka příkazu
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Pokud používáte [WAImportExport nástroj](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) zadejte "AlreadyEncrypted" a zadejte klíče v driveset sdíleného svazku clusteru.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>Další postup

* [Nastavení nástroje WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Ukázka Azure Import, Export REST API](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

