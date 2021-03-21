---
title: Použití Azure import/export k přenosu dat do a z Azure Storage | Microsoft Docs
description: Naučte se vytvářet úlohy importu a exportu v Azure Portal pro přenos dat do a z Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b1f1560fc1a00577e1e4b30d922fc7d4cae0ab92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181829"
---
# <a name="what-is-azure-importexport-service"></a>Co je služba Azure import/export?

Služba import/export Azure slouží k bezpečnému importu velkých objemů dat do služby Azure Blob Storage a souborů Azure prostřednictvím přenosu diskových jednotek do datacentra Azure. Pomocí této služby je také možné přenést data ze služby Azure Blob Storage na diskové jednotky a odeslat je do místních lokalit. Data z jedné nebo více diskových jednotek můžete importovat buď do služby Azure Blob Storage, nebo do souborů Azure.

Zadejte vlastní diskové jednotky a přeneste data pomocí služby Azure import/export. Můžete také použít diskové jednotky dodávané společností Microsoft.

Pokud chcete přenášet data pomocí diskových jednotek poskytovaných Microsoftem, můžete k importu dat do Azure použít [Azure Data box disk](../databox/data-box-disk-overview.md) . Společnost Microsoft dodává až 5 šifrovaných diskových jednotek SSD (SSD) s celkovou kapacitou 40 TB na objednávku do vašeho datacentra prostřednictvím regionálního dopravce. Můžete rychle konfigurovat diskové jednotky, kopírovat data na diskové jednotky prostřednictvím připojení USB 3,0 a diskové jednotky dodávat zpátky do Azure. Další informace najdete v článku [přehled Azure Data box disk](../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Případy použití importu a exportu v Azure

Zvažte použití služby importu a exportu v Azure, pokud je nahrávání nebo stahování dat v síti příliš pomalé, nebo pokud je větší šířka pásma sítě zakazují náklady. Tuto službu použijte v následujících případech:

* **Migrace dat do cloudu**: rychlé a nákladové přesunutí velkých objemů dat do Azure.
* **Distribuce obsahu**: umožňuje rychle odesílat data na zákaznické weby.
* **Zálohování**: pořídit zálohy místních dat, aby se ukládaly v Azure Storage.
* **Obnovení dat**: Obnova velkého množství dat uložených v úložišti a jejich doručování do místního umístění.

## <a name="importexport-components"></a>Komponenty pro import/export

Služba import/export používá následující komponenty:

* **Služba import/export**: Tato služba je dostupná v Azure Portal pomáhá uživateli vytvářet a sledovat úlohy importu a exportu dat (nahrávání) a exportovat (Stáhnout) úlohy.  

* **Nástroj WAImportExport**: Jedná se o nástroj příkazového řádku, který provede následující akce:
  * Připraví diskové jednotky, které jsou dodávány pro import.
  * Usnadňuje kopírování dat na jednotku.
  * Šifruje data na jednotce pomocí AES 256-bit BitLockeru. K ochraně klíče BitLockeru můžete použít ochranu pomocí externího klíče.
  * Vygeneruje soubory deníku jednotky používané při vytváření importu.
  * Pomáhá identifikovat počet jednotek potřebných pro úlohy exportu.

> [!NOTE]
> Nástroj WAImportExport je k dispozici ve dvou verzích verze 1 a 2. Doporučujeme, abyste používali:
>
> * Verze 1 pro import/export do úložiště objektů BLOB v Azure
> * Verze 2 pro import dat do souborů Azure.
>
> Nástroj WAImportExport je kompatibilní pouze s 64bitovým operačním systémem Windows. Konkrétní verze operačních systémů najdete v požadavcích na [Import/export do Azure](storage-import-export-requirements.md#supported-operating-systems).

* **Diskové jednotky**: jednotky SSD (Solid-State Drive) a jednotky pevného disku (HDD) můžete dodávat do datacentra Azure. Při vytváření úlohy importu dodáváte diskové jednotky obsahující vaše data. Při vytváření úlohy exportu dodáváte prázdné jednotky do datacentra Azure. U určitých typů disků přejít na [podporované typy disků](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak funguje služba Import/Export?

Služba import/export Azure umožňuje přenos dat do objektů blob Azure a souborů Azure vytvořením úloh. K vytvoření úloh použijte Azure Portal nebo Azure Resource Manager REST API. Každá úloha je přidružená k jednomu účtu úložiště.

Úlohy mohou být importovány nebo exportovány. Úloha importu umožňuje importovat data do objektů blob Azure nebo souborů Azure, zatímco úloha exportu umožňuje export dat z objektů blob Azure. Pro úlohu importu dodáte jednotky, které obsahují vaše data. Když vytvoříte úlohu exportu, dodáte prázdné jednotky do datacentra Azure. V každém případě můžete odeslat až 10 diskových jednotek na jednu úlohu.

### <a name="inside-an-import-job"></a>V rámci úlohy importu

V rámci vysoké úrovně úloha importu zahrnuje následující kroky:

1. Určete data, která se mají importovat, počet jednotek, které potřebujete, cílové umístění objektu BLOB pro vaše data ve službě Azure Storage.
2. K kopírování dat na diskové jednotky použijte nástroj WAImportExport. Zašifrujte diskové jednotky pomocí nástroje BitLocker.
3. V Azure Portal vytvořte úlohu importu do svého cílového účtu úložiště. Nahrajte soubory deníku jednotek.
4. Zadejte zpáteční adresu a číslo účtu dopravce pro odeslání jednotek zpět.
5. Jednotky disku dodejte na doručovací adresu zadanou během vytváření úlohy.
6. Aktualizujte číslo sledování doručení v podrobnostech úlohy import a odešlete úlohu importu.
7. Jednotky jsou přijímány a zpracovávány v datovém centru Azure.
8. Jednotky se dodávají pomocí vašeho účtu dopravce na zpáteční adresu poskytnutou v úloze importu.

> [!NOTE]
> V případě dodávek místních (v rámci země nebo oblasti datového centra) prosím nasdílejte účet domácího dopravce.
>
> Pro zahraniční dodávky (mimo země/oblast datového centra) prosím nasdílejte mezinárodní účet dopravce.

 ![Obrázek 1: import toku úloh](./media/storage-import-export-service/import-job.png)

Podrobné pokyny k importu dat najdete tady:

* [Import dat do objektů blob Azure](storage-import-export-data-to-blobs.md)
* [Import dat do souborů Azure](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>V rámci úlohy exportu

> [!IMPORTANT]
> Služba podporuje jenom export objektů blob Azure. Export souborů Azure se nepodporuje.

V nejvyšší úrovni úloha exportu zahrnuje následující kroky:

1. Určete, jaká data se mají exportovat, kolik jednotek budete potřebovat, zdrojové objekty blob nebo cesty kontejnerů vašich dat v úložišti objektů BLOB.
2. Vytvořte ve svém účtu zdrojového úložiště úlohu exportu v Azure Portal.
3. Zadejte zdrojové objekty blob nebo cesty kontejneru pro data, která chcete exportovat.
4. Zadejte zpáteční adresu a číslo účtu dopravce pro odeslání jednotek zpět.
5. Jednotky disku dodejte na doručovací adresu zadanou během vytváření úlohy.
6. Aktualizujte číslo sledování doručení v podrobnostech úlohy Export a odešlete úlohu exportu.
7. Jednotky jsou přijímány a zpracovávány v datovém centru Azure.
8. Jednotky jsou šifrovány pomocí nástroje BitLocker a klíče jsou k dispozici prostřednictvím Azure Portal.  
9. Jednotky se dodávají pomocí vašeho účtu dopravce na zpáteční adresu poskytnutou v úloze importu.

> [!NOTE]
> V případě dodávek místních (v rámci země nebo oblasti datového centra) prosím nasdílejte účet domácího dopravce.
>
> Pro zahraniční dodávky (mimo země/oblast datového centra) prosím nasdílejte mezinárodní účet dopravce.
  
 ![Obrázek 2: Export toku úloh](./media/storage-import-export-service/export-job.png)

Podrobné pokyny k exportu dat najdete v tématu [Export dat z objektů blob Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Služba Azure import/export podporuje kopírování dat do a ze všech účtů úložiště Azure. Diskové jednotky můžete dodávat do jednoho z uvedených umístění. Pokud je váš účet úložiště v umístění Azure, které zde není zadáno, je při vytváření úlohy k dispozici alternativní umístění expedice.

### <a name="supported-shipping-locations"></a>Podporovaná umístění expedice

|Země/oblast  |Země/oblast  |Země/oblast  |Země/oblast  |
|---------|---------|---------|---------|
|East US    | Severní Evropa        | Indie – střed        |US Gov – Iowa         |
|USA – západ     |West Europe         | Indie – jih        | US DoD – východ        |
|USA – východ 2    | Východní Asie        |  Západní Indie        | US DoD – střed        |
|Západní USA 2     | Southeast Asia        | Střední Kanada        | Čína – východ         |
|USA – střed     | Austrálie – východ        | Kanada – východ        | Čína – sever        |
|USA – středosever     |  Austrálie – jihovýchod       | Brazílie – jih        | Spojené království – jih        |
|Středojižní USA     | Japonsko – západ        |Jižní Korea – střed         | Německo – střed        |
|USA – středozápad     |  Japonsko – východ       | USA (Gov) – Virginia        | Německo – severovýchod        |
|Jižní Afrika – západ   |  Jižní Afrika – sever |

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Data na jednotce jsou šifrovaná pomocí šifrování AES 256-bit nástroj BitLocker Drive Encryption. Toto šifrování chrání vaše data během přenosu.

Pro úlohy importu se jednotky šifrují dvěma způsoby.  

* Zadejte možnost při použití *dataset.csv* souboru při přípravě jednotky.

* Ručně povolte šifrování BitLockeru na jednotce. Zadejte šifrovací klíč v *driveset.csv* při spuštění nástroje WAImportExport na příkazovém řádku během přípravy jednotky. Šifrovací klíč BitLockeru se dá dále chránit pomocí ochrany externích klíčů (označované taky jako spravovaný klíč Microsoftu) nebo podle zákaznického klíče spravovaného zákazníkem. Další informace najdete v tématu [použití spravovaného klíče zákazníka k ochraně klíče nástroje BitLocker](storage-import-export-encryption-key-portal.md).

Pro úlohy exportu se po zkopírování dat na jednotky tato služba před odesláním zpět do vaší jednotky zašifruje pomocí nástroje BitLocker. Šifrovací klíč vám poskytnete prostřednictvím Azure Portal. Jednotku je potřeba odemknout pomocí nástroje WAImporExport pomocí klíče.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Ceny

**Poplatek za manipulační disk**

Pro každou jednotku zpracovánou jako součást úlohy importu nebo exportu existuje poplatek za zpracování jednotky. Podívejte se na podrobnosti o [cenách za import/export Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Náklady na expedici**

Při dodávání jednotek do Azure platíte náklady na expedici do přepravce. Jakmile Microsoft vrátí jednotky, účtují se náklady na expedici za účet dopravce, který jste zadali v době vytváření úlohy.

**Cena za transakce**

[Standardní poplatek za transakci úložiště](https://azure.microsoft.com/pricing/details/storage/) se používá během importu a také při exportu dat. Při exportu dat z Azure Storage se použijí i poplatky za standardní výstup za transakce služby Storage. Další informace o nákladech na výstup najdete v tématu [ceny za přenos dat.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Další kroky

Naučte se používat službu import/export k těmto akcím:

* [Import dat do objektů blob Azure](storage-import-export-data-to-blobs.md)
* [Export dat z objektů blob Azure](storage-import-export-data-from-blobs.md)
* [Import dat do souborů Azure](storage-import-export-data-to-files.md)
