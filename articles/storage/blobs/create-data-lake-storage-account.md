---
title: Vytvoření účtu úložiště pro Azure Data Lake Storage Gen2
description: Naučte se vytvořit účet úložiště pro použití s Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6b63933fc625079bb490942cf3a32232a484fe38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89270074"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Vytvoření účtu úložiště pro použití s Azure Data Lake Storage Gen2

Pokud chcete používat funkce Data Lake Storage Gen2, vytvořte účet úložiště, který má hierarchický obor názvů.

## <a name="choose-a-storage-account-type"></a>Zvolit typ účtu úložiště

Funkce Data Lake Storage jsou podporované v následujících typech účtů úložiště:

- Účty pro obecné účely verze 2
- BlockBlobStorage

Informace o tom, jak si vybírat mezi nimi, najdete v tématu [Přehled účtu úložiště](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Vytvoření účtu úložiště s hierarchickým oborem názvů

Vytvořte účet pro [obecné účely v2](../common/storage-account-create.md) nebo účet [BlockBlobStorage](storage-blob-create-account-block-blob.md) s povoleným nastavením **hierarchického oboru názvů** .

Odemkněte možnosti Data Lake Storage při vytváření účtu povolením nastavení **hierarchického oboru názvů** na kartě **Upřesnit** na stránce **vytvořit účet úložiště** . Toto nastavení musíte povolit při vytváření účtu. Nemůžete ho později povolit.

Toto nastavení se zobrazuje na stránce **vytvořit účet úložiště** na následujícím obrázku.

> [!div class="mx-imgBorder"]
> ![Nastavení hierarchického oboru názvů](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Pokud máte existující účet úložiště, který chcete používat se službou Data Lake Storage, a nastavení hierarchického oboru názvů je zakázané, musíte migrovat data na nový účet úložiště s povoleným nastavením.

## <a name="next-steps"></a>Další kroky

- [Přehled účtu úložiště](../common/storage-account-overview.md)
- [Použití Azure Data Lake Storage Gen2 pro požadavky na velké objemy dat](data-lake-storage-data-scenarios.md)
- [Řízení přístupu ve službě Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)