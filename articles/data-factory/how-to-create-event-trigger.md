---
title: Vytváření aktivační události na základě událostí v Azure Data Factory | Microsoft Docs
description: Naučte se vytvořit aktivační událost v Azure Data Factory používající kanál v reakci na událost.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: douglasl
ms.openlocfilehash: a9c15b239ee0bd0dde0b1f11691565b2676e3d07
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062117"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Vytvořit aktivační událost, která běží v reakci na událost kanálu

Tento článek popisuje aktivační události na základě událostí, které vytvoříte v objektu pro vytváření dat kanály.

Událostmi řízené architektura (Automatizace elektronického designu) je běžný vzor integraci dat, který zahrnuje výroby, detekce, spotřeby a reakce na události. Scénáře integrace dat často vyžadují zákazníci služby Data Factory k aktivaci na základě událostí kanály. Objekt pro vytváření dat je nyní integrována [mřížky událostí Azure](https://azure.microsoft.com/services/event-grid/), které lze spustíte kanálů na události.

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

### <a name="create-a-new-event-trigger"></a>Vytvořit novou aktivační událost

Je typické událost k doručení souboru, nebo došlo k odstranění souboru, ve vašem účtu úložiště Azure. Můžete vytvořit aktivační událost, která odpovídá k této události v kanálu vaše Data Factory.

> [!NOTE]
> Tato integrace podporuje jenom účty úložiště verze 2 (obecné účely).

![Vytvořit novou aktivační událost](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>Vyberte typ aktivační události

Jakmile soubor dorazí v umístění vašeho úložiště a odpovídající objekt blob se vytvoří, tato událost se aktivuje a spustí vaše kanál služby Data Factory. Můžete vytvořit aktivační událost, která reaguje na vytváření událostí do objektu blob, o událost odstranění objektů blob nebo obou událostí, v kanály Data Factory.

![Vyberte aktivační událost typu jako událost.](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>Konfigurace aktivační události

S **začíná cesta blobu** a **cesta objektu Blob končí** vlastnosti, můžete zadat kontejnery, složky a názvy objektů blob, pro které chcete přijímat události. Řadu vzorů můžete použít pro obě **začíná cesta blobu** a **cesta objektu Blob končí** vlastnosti, jak je znázorněno v příkladech později v tomto článku. Alespoň jeden z těchto vlastností je povinný.

![Konfigurace aktivační události](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>Schématu JSON

Následující tabulka obsahuje přehled elementů schématu, které se vztahují k aktivační události na základě událostí:

| **JSON – Element** | **Popis** | **Typ** | **Povolené hodnoty** | **Požadované** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Obor** | ID prostředku Azure Resource Manager účtu úložiště. | Řetězec | ID správce prostředků Azure | Ano |
| **Události** | Typ události, které způsobují tento generovat aktivační událost. | Pole    | Microsoft.Storage.BlobCreated Microsoft.Storage.BlobDeleted | Ano, libovolnou kombinaci. |
| **blobPathBeginsWith** | Cesta objektu blob musí začínat vzoru zadaná pro generovat aktivační událost. Například '/ záznamy nebo objekty BLOB/prosinec /' bude pouze platit aktivační událost pro objekty BLOB ve složce prosinec v kontejneru záznamy. | Řetězec   | | Je třeba zadat alespoň jednu z těchto vlastností: blobPathBeginsWith blobPathEndsWith. |
| **blobPathEndsWith** | Cesta objektu blob musí končit vzoru zadaná pro generovat aktivační událost. Například 'december/boxes.csv' bude platit pouze aktivační událost pro objekty BLOB s názvem polí ve složce prosinec. | Řetězec   | | Je třeba zadat alespoň jednu z těchto vlastností: blobPathBeginsWith blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Příklady aktivační události na základě událostí

Tato část obsahuje příklady nastavení na základě událostí aktivační události.

-   **Cesta blobu začíná**('/ containername /") – přijímá události pro libovolný objekt blob v kontejneru.
-   **Cesta blobu začíná**('/ containername nebo objekty BLOB/název_složky) – přijímá události pro všechny objekty BLOB v kontejneru containername a název_složky složce.
-   **Cesta blobu začíná**("/ containername/blobs/foldername/file.txt") – přijímá události pro objekt blob s názvem soubor.txt ve složce název_složky v kontejneru containername.
-   **Cesta objektu BLOB končí**('soubor.txt") – Receives události pro objekt blob s názvem soubor.txt na jakoukoli cestu.
-   **Cesta objektu BLOB končí**('/ containername/blobs/file.txt ") – přijímá události pro objekt blob s názvem soubor.txt pod containername kontejneru.
-   **Cesta objektu BLOB končí**('foldername/file.txt') – Receives události pro objekt blob s názvem soubor.txt ve složce název_složky v jakékoli kontejneru.

> [!NOTE]
> Je nutné zahrnout `/blobs/` segment cesty vždy, když zadáte kontejneru a složku, kontejner a soubor nebo kontejner, složku a soubor.

## <a name="using-blob-events-trigger-properties"></a>Pomocí vlastnosti aktivační události objektu Blob

Když se aktivuje aktivační události objektu blob, ho zpřístupní dvě proměnné do kanálu: *folderPath* a *fileName*. Chcete-li získat přístup k těmto proměnným, použijte `@triggerBody().fileName` nebo `@triggerBody().folderPath` výrazy.

Představte si třeba nakonfigurovat tak, aby fire při vytvoření objektu blob se aktivační událost `.csv` jako hodnotu `blobPathEndsWith`. Pokud soubor .csv se ukončí do účtu úložiště *folderPath* a *fileName* popisují umístění souboru CSV. Například *folderPath* má hodnotu `/containername/foldername/nestedfoldername` a *fileName* má hodnotu `filename.csv`.

## <a name="next-steps"></a>Další postup
Podrobné informace o aktivační události najdete v tématu [kanálu spouštěcí a aktivační události](concepts-pipeline-execution-triggers.md#triggers).
