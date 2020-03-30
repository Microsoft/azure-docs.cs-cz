---
title: Případy použití služby Microsoft Azure Data Box Gateway | Dokumenty společnosti Microsoft
description: Popisuje případy použití azure data box gateway, řešení úložiště virtuálních zařízení, které umožňuje přenos dat do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: e72113313e27949819db567c550401b1f051473f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022677"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Případy použití brány datové schránky Azure

Azure Data Box Gateway je zařízení brány cloudového úložiště, které se nachází ve vašich prostorách a odesílá vaše image, média a další data do Azure. Tato brána cloudového úložiště je virtuální počítač zřízený ve vašem hypervisoru. Data do tohoto virtuálního zařízení zapisujete pomocí protokolů NFS a SMB, které pak odešle do Azure. Tento článek obsahuje podrobný popis scénářů, kde můžete nasadit toto zařízení.

Bránu datového schránce použijte pro následující scénáře:

- Chcete-li nepřetržitě ingestovat obrovské množství dat.
- Pro cloudovou archivaci dat bezpečným a efektivním způsobem.
- Pro přírůstkový přenos dat po síti po počátečníhromadný přenos se provádí pomocí Data Box.

Každý z těchto scénářů je podrobně popsán v následujících částech.


## <a name="continuous-data-ingestion"></a>Nepřetržité přijímání dat

Jednou z hlavních výhod brány datové schránky je možnost průběžně ingestovat data do zařízení ke kopírování do cloudu, bez ohledu na velikost dat.

Když se data zapisují do zařízení brány, zařízení nahraje data do Azure Storage. Zařízení automaticky spravuje úložiště odebráním souborů místně při zachování metadat, když dosáhne určité prahové hodnoty. Zachování místní kopie metadat umožňuje zařízení brány nahrát změny pouze při aktualizaci souboru. Data nahraná do zařízení brány by měla být podle pokynů v [upozorněních na nahrání dat](data-box-gateway-limits.md#data-upload-caveats).

Když se zařízení naplní daty, začne omezovat rychlost příchozího přenosu dat (podle potřeby), aby odpovídalo rychlosti, jakou se data nahrávají do cloudu. Chcete-li sledovat nepřetržité ingestování na zařízení, použijte výstrahy. Tyto výstrahy jsou vyvolány po omezení začíná a jsou vymazány po omezení bylo zastaveno.

## <a name="cloud-archival-of-data"></a>Cloudová archivace dat

Bránu datové schránky použijte, pokud chcete data dlouhodobě uchovávat v cloudu. Vrstvu **archivu** úložiště můžete použít pro dlouhodobé uchovávání.

Archivní vrstva je optimalizována pro ukládání zřídka přístupných dat po dobu nejméně 180 dnů. **Archivní** úroveň nabízí nejnižší náklady na úložiště, ale má nejvyšší náklady na přístup. Další informace naleznete na [úrovni přístupu archivu](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Přesunutí dat do úrovně Archivace

Než začnete, ujistěte se, že máte spuštěné zařízení Brány datové schránky. Postupujte podle kroků popsaných v [kurzu: Připravte se na nasazení brány datové schránky Azure](data-box-gateway-deploy-prep.md) a pokračujte v postupu k dalšímu kurzu, dokud nebudete mít provozní zařízení.

- Pomocí zařízení Data Box Gateway můžete odesílat data do Azure obvyklým postupem přenosu, jak je popsáno v části [Přenos dat přes bránu datové schránky](data-box-gateway-deploy-add-shares.md).
- Po nahrání dat je nutné je přesunout do úrovně Archiv. Vrstvu objektu blob můžete nastavit dvěma způsoby: skriptazure powershellu nebo zásady správy životního cyklu úložiště Azure.  
    - Pokud používáte Azure PowerShell, postupujte [takto](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) přesunout data do vrstvy Archive.
    - Pokud používáte Azure Lifecycle Management, postupujte podle těchto kroků přesunout data do úrovně Archiv.
        - [Zaregistrujte se](/azure/storage/common/storage-lifecycle-management-concepts) pro náhled služby správy životního cyklu objektu Blob a použijte úroveň Archiv.
        - Následující zásady slouží k [archivaci dat při ingestování](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- Jakmile jsou objekty BLOB označeny jako Archiv, již je brána nemůže upravovat, pokud nejsou přesunuty do horké nebo studené vrstvy. Pokud je soubor v místním úložišti, nebudou všechny změny provedené v místní kopii (včetně odstranění) odeslány do archivní vrstvy.
- Chcete-li číst data v úložišti archivu, musí být rehydratována změnou úrovně objektu blob na horkou nebo studenou. [Obnovení sdílené složky](data-box-gateway-manage-shares.md#refresh-shares) na bráně není rehydratovat objekt blob.

Další informace najdete v dalších informacích o tom, jak [spravovat životní cyklus úložiště objektů blob Azure](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Počáteční hromadný přenos následovaný přírůstkovým přenosem

Bránu data boxu a datové schránky použijte společně, pokud chcete provést hromadné nahrávání velkého množství dat následovaných přírůstkovými přenosy. Pro hromadný přenos v režimu offline (počáteční počáteční osivo) a bránu datové schránky použijte datovou schránku pro přírůstkové přenosy (průběžné přenosy) po síti.

### <a name="seed-the-data-with-data-box"></a>Osékací data s datovou schránkou

Podle těchto kroků zkopírujte data do datové schránky a nahrajte je do Azure Storage.

1. [Objednejte si datovou schránku](/azure/databox/data-box-deploy-ordered).
2. [Nastavte si datovou schránku](/azure/databox/data-box-deploy-set-up).
3. [Kopírování dat do datové schránky přes SMB](/azure/databox/data-box-deploy-copy-data).
4. [Vraťte datovou schránku a ověřte, zda se data nahrají do Azure](/azure/databox/data-box-deploy-picked-up).
5. Po dokončení nahrávání dat do Azure by všechna data měla být v kontejnerech úložiště Azure. V účtu úložiště pro data box, přejděte na kontejner objektů blob (a soubor) a ujistěte se, že všechna data se zkopíruje. Poznamenejte si název kontejneru, protože tento název budete později používat. Například na následujícím snímku `databox` obrazovky bude kontejner použit pro přírůstkový přenos.

    ![Kontejner s daty na datové schránce](media/data-box-gateway-use-cases/data-container1.png)

Tento hromadný přenos dokončí počáteční fázi výsevu.

### <a name="ongoing-feed-with-data-box-gateway"></a>Průběžný informační kanál s bránou datové schránky

Postupujte podle následujících kroků pro průběžné ingestování podle brány datové schránky.

1. Vytvořte sdílenou složku cloudu v bráně datové schránky. Tato sdílená sdílená složky automaticky nahraje všechna data do účtu Azure Storage. Přejděte na **Sdílené položky** v prostředku brány datové schránky a klikněte na **+ Přidat sdílenou složku**.

    ![Klikněte na +Přidat sdílenou složku.](media/data-box-gateway-use-cases/add-share1.png)

2. Ujistěte se, že tato sdílená analýza se mapuje na kontejner, který obsahuje semenná data. V **poli Vybrat kontejner objektů blob**zvolte Použít **existující** a přejděte do kontejneru, do kterého byla přenesena data z datové schránky.

    ![Sdílení nastavení](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Po vytvoření sdílené složky aktualizujte sdílenou složku. Tato operace aktualizuje místní sdílení s obsahem z Azure.

    ![Aktualizovat sdílenou složku](media/data-box-gateway-use-cases/refresh-share1.png)

    Při synchronizaci sdílené složky brána datové schránky nahraje přírůstkové změny, pokud byly soubory změněny v klientovi.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box Gateway](data-box-gateway-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Gateway](data-box-gateway-limits.md).
- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.