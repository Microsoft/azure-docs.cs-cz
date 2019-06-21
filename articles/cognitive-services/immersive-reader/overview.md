---
title: O moderním Reader
titlesuffix: Azure Cognitive Services
description: Další informace o moderním čtečky
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 38056f701b76f3fc2cf1f617a6977321445f78ff
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296815"
---
# <a name="what-is-immersive-reader"></a>Co je program ke čtení atraktivní?

[Atraktivní čtečky](https://www.onenote.com/learningtools) je (včetně) navržené nástroj, který implementuje osvědčených postupů a zlepšit míru porozumění čtení pro nově vznikající čtečky inteligentních algoritmů jazyka, uživatelé, kteří se studijním rozdíly, jako jsou problémy s učením.

Můžete použít atraktivní čtečky ve webové aplikaci pomocí sady SDK atraktivní čtečky.

## <a name="what-does-immersive-reader-do"></a>Co dělá atraktivní čtečky?

Dokonalé čtečky je navržená tak, aby přístupnější čtení pro všechny uživatele.

* Zobrazí obsah v zobrazení pro minimální čtení

  ![Dokonalé čtečky](./media/immersive-reader.png)

* Zobrazí obrázky běžně používaných výrazů

  ![Obrázek slovník](./media/picture-dictionary.png)

* Zvýrazní podstatná jména, příkazy, přídavných jmen a příslovcí

  ![Částí řeči](./media/parts-of-speech.png)

* Přečte obsah nahlas vám

  ![Rychlost čtení](./media/read-aloud.png)

* Převede obsah do jiného jazyka

  ![Překlad](./media/translation.png)

* Rozdělí slova do slabik

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak funguje atraktivní čtečky?

Je atraktivní čtečky samostatné webové aplikace, která při vyvolání pomocí atraktivní čtečky JavaScript SDK, zobrazí se nad vaší existující webové aplikace pomocí `iframe`. Při volání rozhraní API za účelem spuštění atraktivní čtečky zadejte obsah, který chcete zobrazit v moderním Reader. Naše sada SDK se stará o vytvoření a stylu `iframe` a komunikaci s back-end službu atraktivní čtečky, který zpracovává obsah částí řeči, převod textu na řeč, překlad a tak dále.

## <a name="next-steps"></a>Další postup

Začínáme s atraktivní Reader:

* Zabývat tím, [rychlý start](./quickstart.md)
* Prozkoumejte [atraktivní čtečky SDK na Githubu](https://github.com/Microsoft/immersive-reader-sdk)
* Čtení [atraktivní čtečky odkaz na sadu SDK](./reference.md)