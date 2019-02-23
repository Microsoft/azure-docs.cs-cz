---
title: Azure Data Factory mapování toku dat datové sady
description: Azure Data Factory mapování toku dat má kompatibilitou sepecific datové sady
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: a8e366b14eb73403bfe50af19e4df56dd220d273
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737052"
---
# <a name="mapping-data-flow-datasets"></a>Mapování datového toku datových sad

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Datové sady jsou definující tvar dat, které pracujete ve vašem kanálu služby Data Factory konstrukce. V toku dat řádků a sloupců dat na úrovni vyžaduje definici jemně odstupňovaná datové sady. Stejná hloubka pochopení dat nevyžadují žádné datové sady použité v kanálů toků řízení.

Datové sady v toku dat se používají v transformace zdroje a jímky. Používají se vytvoří definici schémat základní data. Pokud schéma ve vašich datech nemáte, můžete nastavit odchylek schématu na pro zdroje a jímky. Pomocí schématu definice z datové sady budete mít souvisejících datových typů, formáty dat, umístění souboru a informace o připojení z přidružené propojené služby. Metadata z datové sady se zobrazí ve vaší zdrojové transformace jako zdroj "Projekce".

## <a name="dataset-types"></a>Typů datových sad

Aktuálně v toku dat, najdete čtyři typy datové sady:

* Azure SQL DB
* Datový sklad Azure SQL
* Parquet (z ADLS a objektů Blob)
* Text s oddělovači (z nástroji ADLB & objektů Blob)

Tok datové sady dat samostatné *typ zdroje* z *typ propojené služby připojení*. Obvykle ve službě Data Factory vyberte typ připojení (objekt Blob, ADLS atd.) a potom definovat typ souboru v datové sadě. Uvnitř toku dat vybere typů zdroje, které je možné přidružit různé typy připojení propojenou službu.

![Možnosti transformace zdroje](media/data-flow/dataset1.png "zdroje")

## <a name="data-flow-compatible-datasets"></a>Kompatibilní datové sady s toku dat

Při vytváření nové datové sady, je zaškrtávací políčko s popiskem "Datový tok kompatibilní" v pravém horním rohu panelu. Kliknutím na toto tlačítko bude filtrovat pouze datové sady, které lze použít s toky dat. 

## <a name="import-schemas"></a>Importovat schémata

Při importu schématu datové sady se předávají Data, zobrazí se na tlačítko Importovat schéma. Kliknutím na toto tlačítko zobrazíte dvě možnosti: Import ze zdroje nebo importovat z místního souboru. Ve většině případů budete importovat schéma přímo ze zdroje. Nicméně pokud máte existující soubor schématu (soubory Parquet nebo CSV se záhlavími), může odkazovat na místní soubor a Data Factory se budou definovat schéma na základě tohoto souboru schématu.

## <a name="next-steps"></a>Další postup

Začněte tím, že [vytvoření nového toku dat](data-flow-create.md) a přidejte zdroje transformace. Nakonfigurujte datovou sadu pro zdroj.

