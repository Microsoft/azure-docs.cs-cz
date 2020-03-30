---
title: Použití importu a exportu Azure k přenosu dat do a z Azure Storage | Dokumenty společnosti Microsoft
description: Zjistěte, jak na webu Azure Portal vytvářet úlohy importu a exportu pro přenos dat do a z Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/15/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eee0fc2797fbe0666a6b848fde574c7807f47cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282439"
---
# <a name="what-is-azure-importexport-service"></a>Co je služba Import a export Azure?

Služba Azure Import/Export se používá k bezpečnému importu velkého množství dat do úložiště objektů Blob Azure a souborů Azure prostřednictvím odesílání diskových jednotek do datového centra Azure. Tuto službu lze také použít k přenosu dat z úložiště objektů Blob Azure na diskové jednotky a dodávat do místních webů. Data z jedné nebo více diskových jednotek se dá importovat do úložiště objektů Blob Azure nebo do souborů Azure.

Zadejte vlastní diskové jednotky a přenášejte data pomocí služby Azure Import/Export. Můžete také použít diskové jednotky dodané společností Microsoft.

Pokud chcete přenášet data pomocí diskových jednotek dodaných společností Microsoft, můžete k importu dat do Azure použít [disk Azure Data Box.](../../databox/data-box-disk-overview.md) Společnost Microsoft dodává do vašeho datového centra prostřednictvím regionálního operátora až 5 šifrovaných diskových jednotek SSD s celkovou kapacitou 40 TB na objednávku. Můžete rychle nakonfigurovat diskové jednotky, kopírovat data na diskové jednotky přes připojení USB 3.0 a doručovat diskové jednotky zpět do Azure. Další informace najdete v přehledu [disku datové schránky Azure](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Případy použití azure importu a exportu

Zvažte použití služby Azure Import/Export při nahrávání nebo stahování dat v síti je příliš pomalé, nebo získání další šířky pásma sítě je nákladově neúnosné. Tuto službu použijte v následujících scénářích:

* **Migrace dat do cloudu**: Přesuňte velké množství dat do Azure rychle a nákladově efektivně.
* **Distribuce obsahu**: Rychle odesílejte data na weby zákazníků.
* **Zálohování**: Zálohy místních dat můžete ukládat do Azure Storage.
* **Obnova dat**: Obnovte velké množství dat uložených v úložišti a doručujte je do místního umístění.

## <a name="importexport-components"></a>Import/export součástí

Služba importu a exportu používá následující součásti:

* **Služba Import/export**: Tato služba dostupná na webu Azure Portal pomáhá uživateli vytvářet a sledovat import dat (nahrávání) a exportovat (stahovat) úlohy.  

* **WAImportExport nástroj**: Toto je nástroj příkazového řádku, který provádí následující:
  * Připraví diskové jednotky, které jsou dodávány pro import.
  * Usnadňuje kopírování dat na jednotku.
  * Šifruje data na jednotce pomocí 128bitového nástroje BitLocker aes. K ochraně klíče nástroje BitLocker můžete použít externí ochranu klíčů.
  * Generuje soubory deníku jednotek použité při vytváření importu.
  * Pomáhá identifikovat počet jednotek potřebných pro úlohy exportu.

> [!NOTE]
> Nástroj WAImportExport je k dispozici ve dvou verzích, verze 1 a 2. Doporučujeme použít:
>
> * Verze 1 pro import a export do úložiště objektů blob Azure.
> * Verze 2 pro import dat do souborů Azure.
>
> Nástroj WAImportExport je kompatibilní pouze s 64bitovým operačním systémem Windows. U konkrétních podporovaných verzí operačního systému přejděte na požadavky na [import a export Azure](storage-import-export-requirements.md#supported-operating-systems).

* **Diskové jednotky**: Do datového centra Azure můžete dodatového centra Azure doručovat jednotky SSD nebo Pevné disky(HDD). Při vytváření úlohy importu dodáte diskové jednotky obsahující data. Při vytváření úlohy exportu odesíláte prázdné jednotky do datového centra Azure. Konkrétní typy disků naleznete v části [Podporované typy disků](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak funguje služba Import/Export?

Služba Azure Import/Export umožňuje přenos dat do objektů BLOB Azure a souborů Azure vytvářením úloh. K vytváření úloh použijte portál Azure nebo rozhraní REST Azure Resource Manager. Každá úloha je přidružena k jednomu účtu úložiště.

Úlohy mohou být importované nebo exportované úlohy. Úloha importu umožňuje importovat data do objektů Blob Azure nebo souborů Azure, zatímco úloha exportu umožňuje exportovat data z objektů Blob Azure. Pro úlohu importu odesíláte jednotky obsahující data. Když vytvoříte úlohu exportu, dodáte prázdné jednotky do datového centra Azure. V každém případě můžete dobýt až 10 diskových jednotek na úlohu.

### <a name="inside-an-import-job"></a>Uvnitř úlohy importu

Na vysoké úrovni úloha importu zahrnuje následující kroky:

1. Určete data, která mají být importována, počet jednotek, které potřebujete, umístění cílového objektu blob pro vaše data v úložišti Azure.
2. Pomocí nástroje WAImportExport zkopírujte data na diskové jednotky. Zašifrujte diskové jednotky nástrojem BitLocker.
3. Vytvořte úlohu importu v cílovém účtu úložiště na webu Azure Portal. Nahrajte soubory deníku jednotek.
4. Zadejte zpáteční adresu a číslo účtu dopravce pro odeslání jednotek zpět k vám.
5. Diskové jednotky zasávat na dodací adresu poskytnutou při vytváření úloh.
6. Aktualizujte sledovací číslo dodávky v podrobnostech úlohy importu a odešlete úlohu importu.
7. Jednotky jsou přijímány a zpracovávány v datovém centru Azure.
8. Jednotky jsou dodávány pomocí účtu dopravce na zpáteční adresu uvedenou v úloze importu.

> [!NOTE]
> U místních zásilek (v rámci země/oblasti datového centra) sdílejte účet domácího dopravce.
>
> U zásilek v zahraničí (mimo zemi/oblast datového centra) sdílejte účet mezinárodního operátora.

 ![Obrázek 1:Importovat tok úlohy](./media/storage-import-export-service/importjob.png)

Podrobné pokyny k importu dat naleznete na adrese:

* [Import dat do objektů Blob Azure](storage-import-export-data-to-blobs.md)
* [Import dat do souborů Azure](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Uvnitř úlohy exportu

> [!IMPORTANT]
> Služba podporuje pouze export objektů BLOB Azure. Export souborů Azure není podporován.

Na vysoké úrovni úloha exportu zahrnuje následující kroky:

1. Určete data, která mají být exportována, počet jednotek, které potřebujete, zdrojové objekty BLOB nebo cesty kontejnerů dat v úložišti objektů Blob.
2. Vytvořte úlohu exportu ve svém účtu zdrojového úložiště na webu Azure Portal.
3. Určete zdrojové objekty BLOB nebo cesty kontejneru pro export dat.
4. Zadejte zpáteční adresu a číslo účtu dopravce pro odeslání jednotek zpět k vám.
5. Diskové jednotky zasávat na dodací adresu poskytnutou při vytváření úloh.
6. Aktualizujte sledovací číslo dodávky v podrobnostech úlohy exportu a odešlete úlohu exportu.
7. Jednotky jsou přijímány a zpracovávány v datovém centru Azure.
8. Jednotky jsou šifrované pomocí nástroje BitLocker a klíče jsou k dispozici prostřednictvím portálu Azure.  
9. Jednotky jsou dodávány pomocí účtu dopravce na zpáteční adresu uvedenou v úloze importu.

> [!NOTE]
> U místních zásilek (v rámci země/oblasti datového centra) sdílejte účet domácího dopravce.
>
> U zásilek v zahraničí (mimo zemi/oblast datového centra) sdílejte účet mezinárodního operátora.
  
 ![Obrázek 2:Exportovat tok úlohy](./media/storage-import-export-service/exportjob.png)

Podrobné pokyny k exportu dat najdete v [oblasti Export dat z objektů BLOB Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Služba Import a export Azure podporuje kopírování dat do a ze všech účtů úložiště Azure. Diskové jednotky můžete dovést do jednoho z uvedených umístění. Pokud je váš účet úložiště v umístění Azure, který není zadán zde, alternativní umístění expedice je k dispozici při vytváření úlohy.

### <a name="supported-shipping-locations"></a>Podporovaná místa expedice

|Země/region  |Země/region  |Země/region  |Země/region  |
|---------|---------|---------|---------|
|USA – východ    | Severní Evropa        | Indie – střed        |US Gov – Iowa         |
|USA – západ     |Západní Evropa         | Indie – jih        | US DoD – východ        |
|USA – východ 2    | Východní Asie        |  Indie – západ        | US DoD – střed        |
|USA – západ 2     | Jihovýchodní Asie        | Střední Kanada        | Čína – východ         |
|USA – střed     | Austrálie – východ        | Kanada – východ        | Čína – sever        |
|USA – středosever     |  Austrálie – jihovýchod       | Brazílie – jih        | Spojené království – jih        |
|USA – středojih     | Japonsko – západ        |Jižní Korea – střed         | Německo – střed        |
|USA – středozápad     |  Japonsko – východ       | USA (Gov) – Virginia        | Německo – severovýchod        |

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Data na jednotce jsou šifrována pomocí 128bitového šifrování bitlockeru nástrojem AES. Toto šifrování chrání vaše data během přenosu.

U úloh importu jsou jednotky šifrovány dvěma způsoby.  

* Zadejte volbu při použití souboru *dataset.csv* při spuštění nástroje WAImportExport během přípravy jednotky.

* Povolte šifrování nástroje BitLocker ručně na jednotce. Při spuštění příkazového řádku nástroje WAImportExport při přípravě jednotky zadejte šifrovací klíč v souboru *driveset.csv.* Šifrovací klíč nástroje BitLocker lze dále chránit pomocí externího nástroje ochrany klíčů (označované také jako klíč spravovaný společností Microsoft) nebo pomocí klíče spravovaného zákazníkem. Další informace naleznete v [tématu Použití klíče spravovaného zákazníkem k ochraně klíče nástroje BitLocker](storage-import-export-encryption-key-portal.md).

U úloh exportu služba po zkopírování dat na jednotky zašifruje jednotku pomocí nástroje BitLocker před jejich odesláním zpět. Šifrovací klíč se vám poskytuje prostřednictvím portálu Azure. Jednotku je třeba odemknout pomocí nástroje WAImporExport pomocí klíče.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Ceny

**Poplatek za manipulaci s pohonem**

Pro každou jednotku zpracovávanou jako součást importu nebo exportní úlohy se účtuje manipulační poplatek jednotky. Podívejte se na podrobnosti o [cenách Azure importu a exportu](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Náklady na dopravu**

Když odesíláte disky do Azure, zaplatíte náklady na dopravu dopravci. Když vám společnost Microsoft vrátí jednotky, náklady na dopravu se účtují na účet dopravce, který jste zadali v době vytváření úloh.

**Cena za transakce**

[Standardní poplatek za transakci úložiště](https://azure.microsoft.com/pricing/details/storage/) platí během importu a exportu dat. Standardní odchozí poplatky se taky platí spolu s poplatky za transakce úložiště při exportu dat z Azure Storage. Další informace o nákladech na odchozí přenos najdete v [tématu Ceny za přenos dat.](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak používat službu Import/Export k:

* [Import dat do objektů Blob Azure](storage-import-export-data-to-blobs.md)
* [Export dat z objektů BLOB Azure](storage-import-export-data-from-blobs.md)
* [Import dat do souborů Azure](storage-import-export-data-to-files.md)
