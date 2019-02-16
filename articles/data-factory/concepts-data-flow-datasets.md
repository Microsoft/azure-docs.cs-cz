---
title: Azure Data Factory mapování toku dat datové sady
description: Azure Data Factory mapování toku dat má kompatibilitou sepecific datové sady
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ccf4273489d739bb9b0d802b79944efefcd02ff4
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331160"
---
# <a name="mapping-data-flow-datasets"></a>Mapování datového toku datových sad

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Datové sady jsou definující tvar dat, které pracujete ve vašem kanálu služby Data Factory konstrukce. V toku dat řádků a sloupců dat na úrovni vyžaduje definici mnohem více jemně odstupňovaná než vyžaduje datové sady v rámci kanálu řízení toku.

Pomocí datové sady ve službě Flow zdroje dat a transformace jímky pro definování schématu základní data. Pokud schéma ve vašich datech nemáte, můžete nastavit odchylek schématu na pro zdroje a jímky. Pomocí schématu definice z datové sady budete mít souvisejících datových typů, formáty dat, umístění souboru a informace o připojení z přidružené propojené služby.

Aktuálně v toku dat, najdete čtyři datové sady:

* Azure SQL DB
* Datový sklad Azure SQL
* Parquet
* Text s oddělovači

Datové sady toku dat oddělte zdroji *typ* z propojené služby typu připojení. Obvykle ve službě Data Factory vyberte typ připojení (objekt Blob, ADLS atd.) a potom definovat typ souboru v datové sadě. Uvnitř toku dat vybere typů zdroje, které je možné přidružit různé typy připojení propojenou službu.

![Možnosti transformace zdroje](media/data-flow/dataset1.png "zdroje")

Při vytváření nové datové sady, je zaškrtávací políčko s popiskem "Datový tok kompatibilní" v pravém horním rohu panelu. Kliknutím na toto tlačítko bude filtrovat pouze datové sady, které lze použít s toky dat. 

Importovat schémata

Při importu schématu datové sady se předávají Data, zobrazí se na tlačítko Importovat schéma. Kliknutím na toto tlačítko zobrazíte dvě možnosti: Import ze zdroje nebo importovat z místního souboru. Ve většině případů budete importovat schéma přímo ze zdroje. Pokud máte existující soubor schématu, může odkazovat na místní soubor a Data Factory bude definovat schéma na základě tohoto souboru schématu.

