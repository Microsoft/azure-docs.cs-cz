---
title: Používání Azure Import/Export pro přenos dat do a z Azure Storage | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit import a export projektů na webu Azure Portal pro přenos dat do a ze služby Azure Storage.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/11/2018
ms.author: alkohli
ms.openlocfilehash: ab73420d1bfe0dbddcf2a0e3c3dd34203e4bb2d7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008412"
---
# <a name="what-is-azure-importexport-service"></a>Co je služba Azure Import/Export?

Služba Azure Import/Export umožňuje bezpečně importovat velké objemy dat do úložiště objektů Blob v Azure a službou soubory Azure přenosem disků do datacentra Azure. Tato služba také umožňuje přenášet data z úložiště objektů Blob Azure do diskové jednotky a odešlete ji do vašich místních lokalit. Data z jednoho nebo více disků se dají importovat buď do Azure Blob storage nebo Azure Files. 

Služba Import/Export Azure je potřeba zadat vlastní disky. Pokud chcete přenos dat pomocí disků poskytnutých Microsoft, můžete použít disku Azure Data Box pro import dat do Azure. Microsoft se dodává až 5 šifrovanými disky SSD (Solid-State Drive) s kapacitou 40 TB za pořadí do svého datového centra prostřednictvím místní operátora. Můžete rychle konfiguraci disků, zkopírujte data na disky přes připojení USB 3.0 a pošlete disky zpátky do Azure. Další informace najdete v části [disku Azure Data Box přehled](https://docs.microsoft.com/azure/databox/data-box-disk-overview).

## <a name="azure-importexport-usecases"></a>Usecases Azure Import/Export

Zvažte použití služby Azure Import/Export při stahování dat přes síť nebo je příliš pomalé nebo získání další šířka pásma je vysokým nákladům. Pomocí této služby v následujících scénářích:

* **Migrace dat do cloudu**: Přesun velkých objemů dat do Azure rychle a nákladově efektivně.
* **Distribuci obsahu**: rychle posílat data do zákaznické lokality.
* **Zálohování**: trvat zálohy místních dat do úložiště v Azure Storage.
* **Obnovení dat**: velké množství dat uložených v úložišti obnovit a nechat si ho doručit na vaše místní umístění.

## <a name="importexport-components"></a>Import/Export komponenty

Služba Import/Export používá následující součásti:

- **Import/Export**služby: Tato služba k dispozici na webu Azure portal pomáhá uživateli vytvářet a sledovat import a export úloh.  

- **Nástroj WAImportExport**: Toto je nástroj příkazového řádku, který provede následující akce: 
    - Připraví jednotky, které jsou dodávány pro import.
    - Usnadňuje kopírování dat na disk.
    - Šifruje data na disku s Bitlockerem.
    - Generuje soubory deníku jednotky použít během vytváření importu.
    - Pomáhá zjistit počet jednotek, které jsou potřebné pro úlohy exportu.

    Tento nástroj je k dispozici ve dvou, verze 1 a 2. Doporučujeme použít:

    - Verze 1 pro import/export do služby Azure Blob storage. 
    - Verze 2 pro import dat do soubory Azure.

    Nástroj WAImportExport je kompatibilní jenom s operační systém Windows 64-bit. Pro konkrétní verze operačního systému nepodporuje, přejděte na [požadavky Azure Import/Export](storage-import-export-requirements.md#supported-operating-systems).

- **Disky**: můžete zaslat jednotky SSD (Solid-State Drive) a jednotky pevného disku (HDD) do datacentra Azure. Při vytvoření úlohy importu, dodávat disky obsahující data. Při vytvoření úlohy exportu, zasílejte prázdné disky do datacentra Azure. Konkrétní typy na disku, přejděte na [podporované typy disků](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak funguje importu/exportu?

Služba Import/Export Azure umožňuje přenos dat do objektů BLOB Azure a službou soubory Azure vytvořením úlohy. Pomocí webu Azure portal nebo rozhraní REST API Azure Resource Manageru k vytvoření úlohy. Každá úloha je přidružený jeden účet úložiště. 

Úlohy, které lze importovat nebo exportovat úlohy. Úlohy importu umožňuje importovat data do objektů BLOB systému Azure nebo Azure files, že úloha exportu umožňuje data exportují z objektů BLOB Azure. Pro úlohu importu dodávat jednotek obsahujících data. Při vytváření úlohy exportu zasílejte prázdné disky do datacentra Azure. V obou případech můžete zaslat až 10 disků na jednu úlohu.

> [!IMPORTANT]
> Export dat do soubory Azure se nepodporuje.

V této části kroky vysoké úrovně, které jsou součástí import a export úloh jsou popsány. 


### <a name="inside-an-import-job"></a>Uvnitř úlohu importu

Úlohy importu na vysoké úrovni, zahrnuje následující kroky:

1. Určení data mají být importovány, počet jednotek, které potřebujete, cílové objektů blob v umístění pro vaše data ve službě Azure storage.
2. Použijte nástroj WAImportExport ke zkopírování dat do diskové jednotky. Šifrování disků s Bitlockerem.
3. Vytvořte úlohu importu na webu Azure portal v účtu cílového úložiště. Nahrajte soubory deníku jednotky.
2. Zadejte zpáteční adresu a číslo účtu dopravce pro přesouvání jednotky zpět.
3. Dodávejte diskové jednotky na dodací adresu zadali při vytváření úlohy.
4. Aktualizovat doručování číslo v podrobnostech o úloze importu sledování a odešlete úlohu importu.
5. Jednotky přijímaly a zpracovávaly v datovém centru Azure.
6. Jednotky se dodávají pomocí svého účtu dopravce na návratovou adresu součástí úlohy importu.
  
    ![Obrázek toku 1:Import úlohy](./media/storage-import-export-service/importjob.png)

Podrobné pokyny o data importovat, přejděte na:

- [Import dat do objektů BLOB Azure](storage-import-export-data-to-blobs.md)
- [Import dat do soubory Azure](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Uvnitř úlohy exportu

> [!IMPORTANT]
> Služba podporuje pouze exportu objektů BLOB Azure. Exportovat soubory Azure se nepodporuje.

Na vysoké úrovni úlohy exportu zahrnuje následující kroky:

1. Určit data, která mají být exportovány, počet jednotek je potřeba, zdrojové objekty BLOB nebo kontejneru cesty vašich dat v úložišti objektů Blob.
3. Vytvořte úlohu exportu ve vašem účtu zdrojového úložiště na webu Azure portal.
4. Zadejte zdroj objekty BLOB nebo kontejneru cesty pro data, která mají být exportovány.
5. Zadejte zpáteční adresu a číslo účtu dopravce pro přesouvání jednotky zpět.
6. Dodávejte diskové jednotky na dodací adresu zadali při vytváření úlohy.
7. Aktualizovat doručování číslo v podrobnostech o úloze export sledování a odeslání úlohy exportu.
8. Jednotky přijímaly a zpracovávaly v datovém centru Azure.
9. Jednotky jsou šifrované pomocí Bitlockeru a klíče jsou k dispozici prostřednictvím webu Azure portal.  
10. Jednotky se dodávají pomocí svého účtu dopravce na návratovou adresu součástí úlohy importu.
  
    ![Obrázek toku 2:Export úlohy](./media/storage-import-export-service/exportjob.png)

Podrobné pokyny o export dat, přejděte na [Export dat z objektů BLOB Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostupnost v oblastech 

Služba Azure Import/Export podporuje kopírování dat do a ze všech účtů úložiště Azure. Můžete zaslat diskové jednotky na jednu z uvedených umístění. Pokud je váš účet úložiště v umístění Azure, které není specifikováno tady, neposkytujeme přesouvání alternativní umístění při vytváření úlohy.

### <a name="supported-shipping-locations"></a>Nepodporuje přesouvání umístění


|Země  |Země  |Země  |Země  |
|---------|---------|---------|---------|
|USA – východ    | Severní Evropa        | Střed Indie        |US Gov – Iowa         |
|USA – západ     |Západní Evropa         | Indie – jih        | US DoD – východ        |
|Východní USA 2    | Východní Asie        |  Indie – západ        | US DoD – střed        |
|USA – západ 2     | Jihovýchodní Asie        | Kanada – střed        | Čína – východ         |
|USA – střed     | Austrálie – východ        | Kanada – východ        | Čína – sever        |
|Střed USA – sever     |  Austrálie – jihovýchod       | Brazílie – jih        | Velká Británie – jih        |
|Střed USA – jih     | Japonsko – západ        |Jižní Korea – střed         | Německo – střed        |
|Západní střed USA     |  Japonsko – východ       | USA (Gov) – Virginia        | Německo – severovýchod        |


## <a name="security-considerations"></a>Aspekty zabezpečení

Data na disku jsou šifrována pomocí nástroje BitLocker Drive Encryption. Toto šifrování chrání vaše data, i když je při přenosu.

Pro úlohy importu jsou šifrované jednotky dvěma způsoby.  


- Zadejte možnost při použití *dataset.csv* souboru při spuštění nástroje WAImportExport při přípravě jednotky. 

- Povolíte šifrování nástrojem BitLocker na jednotce ručně. Zadejte šifrovací klíč v *driveset.csv* při spouštění WAImportExport nástroj příkazového řádku při přípravě jednotky.


Pro úlohy exportu po zkopírování dat do jednotky, službu šifruje jednotky pomocí Bitlockeru před jeho dodání zpět. Budete mít k dispozici šifrovací klíč prostřednictvím webu Azure portal.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Ceny

**Jednotka poplatek za zpracování**

Je poplatek za zpracování jednotky pro každou jednotku zpracována jako součást import nebo export úloh. Podrobnosti o tom, [ceny služby Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Dopravné**

Jestliže doplníte jednotky do Azure, platíte přesouvání náklady dopravce. Po návratu jednotek Microsoft pro vás přesouvání náklady se účtuje na účet přepravce, který jste zadali v okamžiku vytvoření úlohy.

**Cena za transakce**

Při importu dat do služby Azure Storage nejsou spojené žádné transakční náklady navíc náklady na transakce služby storage úrovně standard. Standardní spojeným se použít při exportu dat z úložiště objektů Blob. Další informace o náklady za transakce, naleznete v tématu [cenách přenosů dat.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>Další postup

Další informace o použití služby Import/Export pro:
* [Import dat do objektů BLOB Azure](storage-import-export-data-to-blobs.md)
* [Export dat z objektů BLOB Azure](storage-import-export-data-from-blobs.md)
* [Import dat do služby soubory Azure](storage-import-export-data-to-files.md)

