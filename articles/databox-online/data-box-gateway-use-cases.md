---
title: Microsoft Azure Data Box Gateway usecases | Dokumentace Microsoftu
description: Popisuje usecases pro Azure Data Box bránu, řešení úložiště virtuálního zařízení, která umožňuje přenášet data do Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 01/17/2019
ms.author: alkohli
ms.openlocfilehash: b4966d03e44591f01bee945c743ea83f57e6da93
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392270"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Případy použití pro Azure Data Box brány

Azure Data Box brány je cloud úložné zařízení brány, který se nachází ve vlastních prostorách a odešle bitové kopie, média a dalších dat do Azure. Tuto bránu cloudové úložiště je zřízené ve vaší hypervisoru virtuálního počítače. Zapisovat data do tohoto virtuálního zařízení, pomocí protokolů systému souborů NFS a protokolu SMB, které pak odešle do Azure. Tento článek poskytuje podrobný popis scénáře, kde můžete nasadit toto zařízení.

Brána pole dat použijte v následujících scénářích:

- Průběžně ingestovat velké objemy dat.
- Pro archivaci dat v zabezpečené a efektivní způsob cloud.
- Pro přenos dat přes síť po počáteční hromadného přenosu nemaže pomocí zařízení Data Box.

Každý z těchto scénářů je podrobně popsány v následujících oddílech.

> [!IMPORTANT]
> Data Box Gateway je ve verzi Preview. Před nasazením tohoto řešení si prosím přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="continuous-data-ingestion"></a>Průběžné ingestování

Jednou z výhod primární brány pole dat je schopnost průběžně ingestovat data do zařízení ke zkopírování do cloudu, bez ohledu na velikost dat.

Jak data se zapisují do zařízení brány, zařízení nahrává data do služby Azure Storage. Zařízení automaticky spravuje úložiště tak, že odeberete soubory místně a přitom zachovat metadata, když dosáhne určité prahové hodnoty. Zachovat místní kopii metadata umožňuje zařízení brány pouze nahrát změny, když dojde k aktualizaci souboru. Data nahraná do vašeho zařízení brány by měla být podle pokynů v [odesílání dat omezením](data-box-gateway-limits.md#data-upload-caveats).

Jak zařízení plní daty, začne omezení rychlost příchozího přenosu dat (podle potřeby) tak, aby odpovídaly rychlost, jakou se data nahrávají do cloudu. K monitorování tak průběžnou příjem dat na zařízení, použijte oznámení. Tyto výstrahy jsou generovány až omezení spustí a zrušte zaškrtnutí až omezení využití sítě se zastavila.

## <a name="cloud-archival-of-data"></a>Archivace dat cloudu

Brána pole dat použijte, pokud chcete uchovávat data pro dlouhodobé v cloudu. Můžete použít **archivu** vrstvu úložiště pro dlouhodobé uchovávání.

Archivní úroveň je optimalizovaná pro ukládání zřídka dat nejméně na 180 dnů. **Archivu** nabízí nejnižší náklady na úložiště, ale má nejvyšší náklady na přístup. Další informace najdete v části [archivní úroveň přístupu](/azure/storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Přesun dat do archivní úrovně

Než začnete, ujistěte se, že máte spuštěné zařízení Data Box brány. Postupujte podle kroků uvedených v [kurzu: Příprava na nasazení Azure Data Box Gateway](data-box-gateway-deploy-prep.md) a zachovat přechodu k dalšímu kurzu, dokud nebudete mít provozní zařízení.

- Použijte k odesílání dat do Azure prostřednictvím obvykle přenos postup, jak je popsáno v zařízení Data Box brány [přenos dat přes bránu dat pole](data-box-gateway-deploy-add-shares.md).
- Po nahrání dat je potřeba ho přesunout do archivní úrovně. Na úrovni objektů blob můžete nastavit dvěma způsoby: Skript Azure Powershellu nebo zásady správy životního cyklu úložiště Azure.  
    - Pokud pomocí prostředí Azure PowerShell, postupujte podle těchto [kroky](/azure/databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) pro přesun dat do archivní úrovně.
    - Pokud používáte správu životního cyklu Azure, použijte následující postup přesun dat do archivní úrovně.
        - [Zaregistrujte](/azure/storage/common/storage-lifecycle-management-concepts.md#register-for-preview) ve verzi Preview služby správy životního cyklu objektu Blob použití archivní úroveň.
        - Použít tyto zásady na [archivovat data na ingestování](/azure/storage/blobs/storage-lifecycle-management-concepts.md#archive-data-at-ingest.md).
- Jakmile objekty BLOB jsou označeny jako archiv, se může nelze nadále upravovat bránou dokud nebudou přesunuty do horké nebo studené úrovně. Pokud je soubor v místním úložišti, všechny změny místní kopie (včetně odstraní) nejsou nahrát do archivní úrovně.
- Číst data v archivním úložišti, musí být rehydrated změnou úrovně objektu blob na horkou nebo studenou. [Aktualizace sdílené složky](data-box-gateway-manage-shares.md#refresh-shares) na bráně dosazení není objekt blob.

Další informace najdete další informace o tom, jak [spravovat Azure Blob Storage cyklu](/azure/storage/common/storage-lifecycle-management-concepts.md).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Počáteční hromadného přenosu, za nímž následuje přírůstkového přenosu

Data Box a pole bránu dat používejte společně Pokud chcete provést hromadné načtení velkého množství dat, za nímž následuje přírůstkové přenosy. Použijte Data Box pro hromadného přenosu do offline režimu (počátečních) a brána pole dat pro přírůstkové přenosy (probíhající informačního kanálu) přes síť.

### <a name="seed-the-data-with-data-box"></a>Počáteční hodnoty data s využitím zařízení Data Box

Postupujte podle těchto kroků ke zkopírování dat do zařízení Data Box a nahrajte do služby Azure Storage.

1. [Pořadí Data Box](/azure/databox/data-box-deploy-ordered.md).
2. [Nastavení vašeho zařízení Data Box](/azure/databox/data-box-deploy-set-up.md).
3. [Kopírování dat do služby Data Box přes SMB](/azure/databox/data-box-deploy-copy-data.md).
4. [Ověřte nahrání dat do Azure, vrátí Data Box](/azure/databox/data-box-deploy-picked-up.md).
5. Po dokončení nahrávání dat do Azure se všechna data by mělo být v kontejnerech služby Azure storage. V účtu úložiště pro Data Box přejděte do kontejneru objektů Blob (a soubor) abyste měli jistotu, že se data kopírují. Poznamenejte si název kontejneru Ujistěte se, jak bude tento název použijete později. Například v následujícím snímku obrazovky `databox` kontejneru se použije pro přírůstkového přenosu.

    ![Kontejner s daty na zařízení Data Box](media/data-box-gateway-use-cases/data-container1.png)

Tento přenos hromadných dokončí počáteční fázi osazení.

### <a name="ongoing-feed-with-data-box-gateway"></a>Probíhající informační kanál s bránou Data Box

Následujícím postupem pro probíhající příjem brána dat pole.

1. Vytvořte sdílenou složku cloud na bráně Data Box. Tuto sdílenou složku automaticky nahrává data do účtu Azure Storage. Přejděte na **sdílené složky** prostředku brány dat pole a klikněte na **+ přidat sdílenou složku**.

    ![Klikněte na tlačítko + Přidat sdílenou složku](media/data-box-gateway-use-cases/add-share1.png)

2. Zajistěte, aby že tuto sdílenou složku se mapuje na kontejner, který obsahuje dosazená data. Pro **kontejner objektů blob vyberte**, zvolte **použít existující** a přejděte do kontejneru, ve kterém byla přenesená data ze zařízení Data Box.

    ![Nastavení sdílené složky](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Po vytvoření sdílené složky obnovit sdílenou složku. Tato operace aktualizuje místní sdílenou složku s obsahem z Azure.

    ![Aktualizovat sdílenou složku](media/data-box-gateway-use-cases/refresh-share1.png)

    Při sdílené složky se synchronizuje, bude brána pole dat nahrát přírůstkové změny, pokud soubory byly změněny na straně klienta.

## <a name="next-steps"></a>Další postup

- Přečtěte si [systémové požadavky služby Data Box Gateway](data-box-gateway-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Gateway](data-box-gateway-limits.md).
- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.