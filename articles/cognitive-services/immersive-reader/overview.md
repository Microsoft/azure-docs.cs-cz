---
title: Co je rozhraní API pro moderní čtečku?
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro moderní čtečku je nástroj, který se dá použít k obnově informací o rozdílech učení nebo k novým čtenářům a nástrojům pro jazyky.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 3e6889cd5d26b88cedd6b110510cb89777252f54
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904364"
---
# <a name="what-is-immersive-reader"></a>Co je Asistivní čtečka?

[Moderní čtečka](https://www.onenote.com/learningtools) je často navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení pro nově vznikající čtenáře, jazyky jazyků a lidi s rozdíly v učení, jako je dyslexia.

Asistivní čtečku můžete využít ve vašich webových aplikacích prostřednictvím sady SDK pro Asistivní čtečku.

## <a name="what-does-immersive-reader-do"></a>Co dělá moderní čtečka?

Moderní čtečka je navržená tak, aby pro každého mohla být přičtená.

* Zobrazuje obsah v minimálním zobrazení pro čtení.

  ![Asistivní čtečka](./media/immersive-reader.png)

* Zobrazí obrázky běžně používaných slov.

  ![Slovník obrázků](./media/picture-dictionary.png)

* Zvýrazní podstatná jména, slovesa, přídavné jména a příslovy.

  ![Části řeči](./media/parts-of-speech.png)

* Přečte obsah po nahlasu

  ![Číst nahlas](./media/read-aloud.png)

* Přeloží obsah do jiného jazyka.

  ![Překlad](./media/translation.png)

* Rozdělí slova na slabiky.

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak funguje moderní čtečka?

Moderní čtečka je samostatná webová aplikace, která při vyvolání pomocí sady SDK pro moderní čtečku se zobrazuje na vaší stávající webové aplikaci prostřednictvím `iframe`. Když zavoláte rozhraní API za účelem spuštění moderního čtecího modulu, zadáte obsah, který chcete zobrazit v moderní čtečce. Naše sada SDK zpracovává vytváření a stylování `iframe` a komunikaci se službou moderního čtecího zařízení, která zpracovává obsah pro části řeči, převod textu na řeč, překlad atd.

## <a name="next-steps"></a>Další kroky

Začínáme s Asistivní čtečkou:

* Přechod k [rychlému](./quickstart.md) startu
* Prozkoumejte [sadu moderních čtenářů na GitHubu](https://github.com/microsoft/immersive-reader-sdk)
* Přečtěte si [referenční informace k sadě pro moderní čtečku SDK](./reference.md)