---
title: Používání Azure Import/Export pro přenos dat do a z Azure Storage | Microsoft Docs
description: Naučte se vytvořit import a export úloh na portálu Azure pro přenos dat do a z Azure Storage.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 83ba437e699eb150e86e6c89e478377394966419
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809538"
---
# <a name="what-is-azure-importexport-service"></a>Co je služba Azure Import/Export?

Služba Azure Import/Export se používá k bezpečně importovat velké objemy dat do úložiště objektů Blob v Azure a Azure soubory pomocí přesouvání disků o datovém centru Azure. Tato služba slouží také k přenosu dat z Azure Blob storage diskových jednotek a dodávat místní servery. Buď do úložiště objektů Blob v Azure nebo Azure soubory můžete importovat data z jednoho nebo více disků. 

## <a name="azure-importexport-usecases"></a>Usecases Azure Import/Export

Zvažte použití služby Azure Import/Export při nahrávání nebo stahování dat přes síť je příliš pomalé nebo získání další šířku pásma sítě je cenovou konkurenceschopnost. Pomocí této služby v následujících scénářích:

* **Migrace dat do cloudu**: rychle přesouvat velké objemy dat do Azure a nákladově efektivní.
* **Distribuci obsahu**: rychle posílat data do lokalit zákazníka.
* **Zálohování**: záloh vaše místní data ukládat ve službě Azure Storage.
* **Obnovení dat**: obnovení velké množství dat uložených v úložišti a nastavit doručení vaše místní umístění.

## <a name="importexport-components"></a>Import a Export součásti

Import a Export služba používá následující součásti:

- **Import a Export**služby: uživateli vytvářet a sledovat import a export úloh pomáhá tato služba k dispozici na portálu Azure.  

- **Nástroj WAImportExport**: to je nástroj příkazového řádku, který provede následující akce: 
    - Připraví vaše jednotky, které jsou součástí pro import.
    - Zajišťuje kopírování dat na disk.
    - Šifruje data na jednotce s nástrojem BitLocker.
    - Generuje soubory deníku jednotky použít během vytváření importu.
    - Pomáhá identifikovat počet jednotek, které jsou potřebné pro úlohy exportu.

    Tento nástroj je k dispozici ve dvou, verze 1 a 2. Doporučujeme vám, že používáte:

    - Verze 1 pro import a export do Azure Blob storage. 
    - Verze 2 pro import dat do Azure souborů.

    Nástroj WAImportExport je jenom kompatibilní s operačním systémem Windows 64-bit. Pro konkrétní verze operačního systému podporován, přejděte na [požadavky na Azure Import/Export](storage-import-export-requirements.md#supported-operating-systems).

- **Disky**: můžete zaslat disků SSD (Solid-State Drive) nebo jednotky pevného disku (HDD) do datového centra Azure. Při vytváření úlohy importu, můžete dodávat disky obsahující data. Při vytváření úlohy exportu, dodáte prázdný jednotek pro datové centrum Azure. Konkrétní typy na disku, přejděte na [podporované typy disku](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak funguje importu a exportu?

Služba Azure Import/Export umožňuje přenos dat do objektů BLOB Azure a Azure Files vytvořením úlohy. Pomocí portálu Azure nebo REST API služby Azure Resource Manager můžete vytvořit úlohy. Každá úloha je přidružený k účtu jedno úložiště. 

Úlohy mohou být import nebo export úloh. Úloha importu umožňuje importovat data do objektů BLOB služby Azure nebo Azure soubory, zatímco úloha exportu umožňuje data mají být exportovány z Azure BLOB. Pro úlohu importu dodávat jednotek obsahujících data. Při vytváření úlohy exportu dodáte prázdný jednotky datového centra Azure. V každém případě můžete zaslat až 10 diskové jednotky na úlohu.

> [!IMPORTANT]
> Export dat do Azure souborů není podporován.

V této části kroky vysoké úrovně, které jsou součástí import a export úloh jsou popsány. 


### <a name="inside-an-import-job"></a>Uvnitř úlohy importu

Na vysoké úrovni úloha importu zahrnuje následující kroky:

1. Určete data mají být importovány, počet jednotek, které potřebujete, cílové umístění objektu blob pro vaše data v úložišti Azure.
2. Použijte nástroj WAImportExport zkopírovat data do diskové jednotky. Šifrování disků s nástrojem BitLocker.
3. Vytvoření úlohy importu v cílový účet úložiště na portálu Azure. Nahrajte soubory deníku jednotky.
2. Zadejte zpáteční adresu a číslo účtu poskytovatel pro přesouvání zpět do jednotky.
3. Diskové jednotky na adresu přenosů zadali při vytvoření úlohy odeslání.
4. Aktualizujte doručení sledování Číslo v podrobnostech úlohy importu a odeslání úlohy importu.
5. Disky jsou přijata a zpracovat datového centra Azure.
6. Jednotky jsou dodávané pomocí účtu poskytovatel návratové adresy uvedené v úloze importu.
  
    ![Obrázek toku 1:Import úlohy](./media/storage-import-export-service/importjob.png)

Podrobné pokyny týkající se data importovat, přejděte na:

- [Importovat data do objektů BLOB služby Azure](storage-import-export-data-to-blobs.md)
- [Importovat data do Azure Files](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Uvnitř úlohy exportu

> [!IMPORTANT]
> Služba podporuje pouze export objektů BLOB Azure. Export Azure souborů není podporován.

Na vysoké úrovni úlohy exportu zahrnuje následující kroky:

1. Určete data, která mají být exportovány, počet jednotek je nutné, zdroj objektů BLOB nebo kontejneru cesty vašich dat v úložišti objektů Blob.
3. Vytvoření úlohy exportu ve vašem účtu úložiště zdroje na portálu Azure.
4. Zadejte zdroj objektů BLOB nebo kontejneru cesty pro data, která mají být exportovány.
5. Zadejte zpáteční adresu a číslo účtu poskytovatel pro přesouvání zpět do jednotky.
6. Diskové jednotky na adresu přenosů zadali při vytvoření úlohy odeslání.
7. Aktualizujte doručení číslo v podrobnostech úlohy exportu sledování a odeslání úlohy exportu.
8. Jednotky jsou přijme a zpracuje v datového centra Azure.
9. Jednotky jsou šifrované pomocí nástroje BitLocker a klíče jsou k dispozici prostřednictvím portálu Azure.  
10. Jednotky jsou dodávané pomocí účtu poskytovatel návratové adresy uvedené v úloze importu.
  
    ![Obrázek toku 2:Export úlohy](./media/storage-import-export-service/exportjob.png)

Podrobné pokyny k exportu dat, přejděte na [exportovat data z Azure blob](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostupnost v oblastech 

Služba Azure Import/Export podporuje kopírování dat do a ze všech účtů úložiště Azure. Můžete zaslat diskové jednotky na jednu z uvedených umístění. Pokud váš účet úložiště v Azure umístění, které není zadané v tomto poli, je k dispozici umístění alternativní přesouvání při vytvoření úlohy.

### <a name="supported-shipping-locations"></a>Podporované přenosů umístění


|Země  |Země  |Země  |Země  |
|---------|---------|---------|---------|
|Východ USA    | Severní Evropa        | Střed Indie        |USA (Gov) – Iowa         |
|Západní USA     |Západní Evropa         | Indie – jih        | US DoD – východ        |
|Východní USA 2    | Východní Asie        |  Indie – západ        | US DoD – střed        |
|Západní USA 2     | Jihovýchodní Asie        | Střední Kanada        | Čína – východ         |
|Střed USA     | Austrálie – východ        | Východní Kanada        | Čína – sever        |
|Střed USA – sever     |  Austrálie – jihovýchod       | Brazílie – jih        | Spojené království – jih        |
|Střed USA – jih     | Japonsko – západ        |Korea – střed         | Německo – střed        |
|Západní střed USA     |  Japonsko – východ       | USA (Gov) – Virginia        | Německo – severovýchod        |


## <a name="security-considerations"></a>Aspekty zabezpečení

Data na disku jsou šifrována pomocí nástroje BitLocker Drive Encryption. Toto šifrování chrání vaše data, i když je při přenosu.

Pro import úlohy jsou šifrované jednotky dvěma způsoby.  


- Zadejte možnost při použití *dataset.csv* soubor při spouštění nástroje WAImportExport během přípravy na jednotku. 

- Povolíte šifrování nástrojem BitLocker na jednotce ručně. Zadejte šifrovací klíč v *driveset.csv* během přípravy na jednotce při spuštění WAImportExport nástroj příkazového řádku.


Pro export úlohy po zkopírování dat na discích službu šifruje jednotky pomocí Bitlockeru před přesouvání zpět do. Šifrovací klíč je poskytnutá prostřednictvím portálu Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Ceny

**Jednotka poplatek za zpracování**

Je poplatek za zpracování jednotky pro každou jednotku zpracovat jako součást import nebo export úlohy. Zobrazit podrobnosti na [Azure Import/Export ceny](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Přesouvání náklady**

Jestliže doplníte jednotky do Azure, platíte náklady na přesouvání poskytovatel přesouvání. Když se Microsoft vrátí jednotky pro vás, náklady na přesouvání je zodpovědné za poskytovatel účet, který jste zadali při vytvoření úlohy.

**Cena za transakci**

Při importu dat do Azure Storage neexistují žádné cena za transakci kromě standardního úložiště cena za transakci. Standardní výstupní poplatky se dají použít při exportu dat z úložiště objektů Blob. Další informace o cena za transakci najdete v tématu [přenos dat se ceny.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>Další postup

Naučte se používat službu Import/Export, aby:
* [Import dat do objektů BLOB služby Azure](storage-import-export-data-to-blobs.md)
* [Exportovat data z objektů BLOB služby Azure](storage-import-export-data-from-blobs.md)
* [Import dat do Azure Files](storage-import-export-data-to-files.md)

