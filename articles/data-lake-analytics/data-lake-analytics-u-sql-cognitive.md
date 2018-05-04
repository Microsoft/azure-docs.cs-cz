---
title: V Azure Data Lake Analytics pomocí U-SQL kognitivní funkcí | Microsoft Docs
description: Další informace o použití intelligence kognitivní funkcí v U-SQL
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: cd06e1ae56efdfdcfcd4fec5b2c17ee843d9e9dd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Kurz: Začínáme s kognitivní možnosti U-SQL

## <a name="overview"></a>Přehled
Kognitivní možnosti U-SQL umožňují vývojářům používat put intelligence ve svých aplikacích velkých objemů dat. 

K dispozici jsou následující možnosti kognitivní:
* Vytvoření bitové kopie: Zjištění řezy
* Vytvoření bitové kopie: Zjištění rozpoznávání emocí úrovně
* Vytvoření bitové kopie: Zjišťovat objekty (označování)
* Vytvoření bitové kopie: Rozpoznávání znaků (optické rozpoznávání znaků)
* Text: Rozbalení klíče frázi
* Text: Analýza postojích

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Jak používat Cognitive ve vašem skriptu U-SQL

Celkový proces je jednoduchý:

* Použití `REFERENCE ASSEMBLY` příkaz k zajištění funkcí, kognitivní pro skript U-SQL
* Použití `PROCESS` ve vstupní sadě řádků pomocí kognitivní UDO, generovat výstup sady řádků

### <a name="detecting-objects-in-images"></a>Zjišťování objektů v obrázcích

Následující příklad ukazuje, jak používat kognitivní funkce ke zjišťování objektů s obrázky.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
Další příklady, podívejte se na **U-SQL nebo Cognitive ukázky** v **další kroky** části.

## <a name="next-steps"></a>Další postup
* [U-SQL nebo kognitivní ukázky](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Pro úlohy Azure Data Lake Analytics pomocí U-SQL okno funkce](data-lake-analytics-use-window-functions.md)
