---
title: Azure Data Factory mapování toku dat datové sady
description: Azure Data Factory mapování toku dat má kompatibilitou sepecific datové sady
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408744"
---
# <a name="mapping-data-flow-datasets"></a>Mapování datového toku datových sad

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Datové sady jsou definující tvar dat, které pracujete ve vašem kanálu služby Data Factory konstrukce. V toku dat řádků a sloupců dat na úrovni vyžaduje definici jemně odstupňovaná datové sady. Stejná hloubka pochopení dat nevyžadují žádné datové sady použité v kanálů toků řízení.

Datové sady ve službě Flow zdroje dat a transformace jímky se používají k definování schématu základní data. Pokud schéma ve vašich datech nemáte, můžete nastavit odchylek schématu na pro zdroje a jímky. Pomocí schématu definice z datové sady budete mít souvisejících datových typů, formáty dat, umístění souboru a informace o připojení z přidružené propojené služby.

## <a name="dataset-types"></a>Typů datových sad

Aktuálně v toku dat, najdete čtyři typy datové sady:

* Azure SQL DB
* Datový sklad Azure SQL
* Parquet
* Text s oddělovači

Datové sady toku dat oddělte zdroji *typ* z propojené služby typu připojení. Obvykle ve službě Data Factory vyberte typ připojení (objekt Blob, ADLS atd.) a potom definovat typ souboru v datové sadě. Uvnitř toku dat vybere typů zdroje, které je možné přidružit různé typy připojení propojenou službu.

![Možnosti transformace zdroje](media/data-flow/dataset1.png "zdroje")

## <a name="data-flow-compatible-datasets"></a>Kompatibilní datové sady s toku dat

Při vytváření nové datové sady, je zaškrtávací políčko s popiskem "Datový tok kompatibilní" v pravém horním rohu panelu. Kliknutím na toto tlačítko bude filtrovat pouze datové sady, které lze použít s toky dat. 

## <a name="import-schemas"></a>Importovat schémata

Při importu schématu datové sady se předávají Data, zobrazí se na tlačítko Importovat schéma. Kliknutím na toto tlačítko zobrazíte dvě možnosti: Import ze zdroje nebo importovat z místního souboru. Ve většině případů budete importovat schéma přímo ze zdroje. Nicméně pokud máte existující soubor schématu (soubory Parquet nebo CSV se záhlavími), může odkazovat na místní soubor a Data Factory se budou definovat schéma na základě tohoto souboru schématu.

