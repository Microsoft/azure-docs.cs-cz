---
title: Microsoft Azure Data Box Gateway případy použití | Microsoft Docs
description: Popisuje případy použití Azure Data Box Gateway, řešení úložiště virtuálního zařízení, které umožňuje přenos dat do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 3bf137f968082e677f45c20947793232b9181220
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786608"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Případy použití pro Azure Data Box Gateway

Azure Data Box Gateway je zařízení brány cloudového úložiště, které se nachází ve vašich prostorách a odesílá vaše image, média a další data do Azure. Tato brána cloudového úložiště je virtuální počítač zřízený v hypervisoru. Do tohoto virtuálního zařízení zapisujete data pomocí NFS a protokolů SMB, které pak pošle do Azure. Tento článek poskytuje podrobný popis scénářů, ve kterých můžete nasadit toto zařízení.

Použijte Data Box Gateway pro následující scénáře:

- Pro nepřetržitou příjem obrovských objemů dat.
- Pro cloudovou archivaci dat zabezpečeným a efektivním způsobem.
- Pro přírůstkové přenosy dat přes síť po počátečním hromadném přenosu pomocí Data Box.

Každý z těchto scénářů je podrobně popsán v následujících částech.


## <a name="continuous-data-ingestion"></a>Průběžné přijímání dat

Jednou z hlavních výhod Data Box Gateway je schopnost nepřetržitě ingestovat data do zařízení ke zkopírování do cloudu bez ohledu na velikost dat.

Při zápisu dat do zařízení brány zařízení nahraje data do Azure Storage. Zařízení automaticky spravuje úložiště odstraněním souborů místně a při dosažení určité prahové hodnoty uchovávají metadata. Udržování místní kopie metadat umožňuje zařízení brány nahrávat změny pouze při aktualizaci souboru. Data nahraná do zařízení brány by měla být podle pokynů v části [Upozornění na nahrávání dat](data-box-gateway-limits.md#data-upload-caveats).

Když se zařízení vyplní daty, začne omezovat přenos dat (podle potřeby), aby odpovídala sazbě, s jakou se data nahrávají do cloudu. Pokud chcete monitorovat průběžné přijímání na zařízení, použijte výstrahy. Tyto výstrahy se aktivují, jakmile se omezování spustí a po zastavení omezení se vymažou.

## <a name="cloud-archival-of-data"></a>Archivace dat v cloudu

Použijte Data Box Gateway, pokud chcete uchovávat data dlouhodobě v cloudu. K dlouhodobému uchovávání můžete použít archivní vrstvu úložiště.

Archivní úroveň je optimalizovaná tak, aby ukládala zřídka používaná data minimálně 180 dní. Úroveň archivu nabízí nejnižší náklady na úložiště, ale má nejvyšší náklady na přístup. Další informace najdete v postupném [přístupu do archivní úrovně](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="move-data-to-the-archive-tier"></a>Přesunout data do archivní úrovně

Než začnete, ujistěte se, že máte spuštěný Data Box Gateway zařízení. Postupujte podle kroků popsaných v tomto [kurzu: Příprava na nasazení Azure Data box Gateway](data-box-gateway-deploy-prep.md) a zachování dalšího kurzu, dokud nebudete mít provozní zařízení.

- Pomocí zařízení Data Box Gateway můžete do Azure nahrávat data prostřednictvím běžného postupu přenosu, jak je popsáno v tématu [přenos dat prostřednictvím data box Gateway](data-box-gateway-deploy-add-shares.md).
- Po nahrání dat je budete muset přesunout do archivní úrovně. Úroveň objektu blob můžete nastavit dvěma způsoby: pomocí skriptu Azure PowerShell nebo zásady správy životního cyklu Azure Storage.  
    - Pokud používáte Azure PowerShell, přesuňte data do archivní úrovně pomocí těchto [kroků](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) .
    - Pokud používáte správu životního cyklu Azure, přesuňte data do archivní úrovně pomocí těchto kroků.
        - [Zaregistrujte](../storage/blobs/storage-lifecycle-management-concepts.md) se do verze Preview služby správy životního cyklu objektů blob, abyste používali archivní vrstvu.
        - K [archivaci dat při](../storage/blobs/storage-lifecycle-management-concepts.md#archive-data-after-ingest)ingestování použijte následující zásady.
- Jakmile jsou objekty blob označeny jako archivní, nemůže je brána nadále měnit, pokud nejsou přesunuty na horkou nebo studenou úroveň. Pokud je soubor v místním úložišti, všechny změny místní kopie (včetně odstranění) se neodesílají do archivní úrovně.
- Chcete-li číst data v archivním úložišti, je nutné data znovu dehydratované změnou úrovně objektu BLOB na horkou nebo studenou. [Obnovením sdílené složky](data-box-gateway-manage-shares.md#refresh-shares) v bráně nebude objekt BLOB znovu vysušen.

Další informace najdete v tématu Jak [Spravovat životní cyklus Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Počáteční hromadný přenos následovaný přírůstkovým přenosem

Data Box a Data Box Gateway používejte společně, pokud chcete provést hromadné nahrávání velkého množství dat, za kterými následují přírůstkové převody. Použijte Data Box pro hromadný přenos v režimu offline (počáteční počáteční hodnotu) a Data Box Gateway přírůstkových přenosů (pokračujících kanálů) po síti.

### <a name="seed-the-data-with-data-box"></a>Dosazení dat pomocí Data Box

Pomocí těchto kroků zkopírujte data Data Box a nahrajte je do Azure Storage.

1. [Seřazení data box](../databox/data-box-deploy-ordered.md).
2. [Nastavte data box](../databox/data-box-deploy-set-up.md).
3. [Kopírovat data do data box přes protokol SMB](../databox/data-box-deploy-copy-data.md).
4. [Vraťte data box a ověřte, že se data nahrávají do Azure](../databox/data-box-deploy-picked-up.md).
5. Po dokončení nahrávání dat do Azure se všechna data musí nacházet v kontejnerech úložiště Azure. V účtu úložiště pro Data Box otevřete kontejner objektů BLOB (a File), abyste se ujistili, že se zkopírují všechna data. Poznamenejte si název kontejneru, podle kterého budete tento název používat později. Například na následujícím snímku obrazovky `databox` se kontejner použije pro přírůstkové přenosy.

    ![Kontejner s daty v Data Box](media/data-box-gateway-use-cases/data-container.png)

Tento hromadný přenos dokončí prvotní fázi osazení.

### <a name="ongoing-feed-with-data-box-gateway"></a>Průběžný informační kanál s Data Box Gateway

Pro průběžné přijímání Data Box Gateway použijte následující postup. 

1. Vytvořte cloudovou sdílenou složku na Data Box Gateway. Tato sdílená složka automaticky nahraje všechna data na účet Azure Storage. Přejděte na **sdílené složky** v prostředku data box Gateway a klikněte na **+ Přidat sdílenou složku**.

    ![Klikněte na + přidat sdílenou složku.](media/data-box-gateway-use-cases/add-share.png)

2. Ujistěte se, že se tato sdílená složka mapuje na kontejner obsahující dosazený data. V části **Vybrat kontejner objektů BLOB** zvolte **použít existující** a přejděte do kontejneru, ve kterém se přenesla data z data box.

    ![Nastavení sdílení](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Po vytvoření sdílené složky aktualizujte sdílenou složku. Tato operace aktualizuje místní sdílenou složku s obsahem z Azure.

    ![Aktualizovat sdílenou složku](media/data-box-gateway-use-cases/refresh-share.png)

    Pokud je sdílená složka synchronizovaná, Data Box Gateway odešle přírůstkové změny, pokud byly soubory v klientovi změněny.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box Gateway](data-box-gateway-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Gateway](data-box-gateway-limits.md).
- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.