---
title: Co je atraktivní čtečky API?
titleSuffix: Azure Cognitive Services
description: Další informace o moderním čtečky rozhraní API.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 4500b6213c549ab9977fe8f2d849ffa8089d04b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718429"
---
# <a name="what-is-immersive-reader"></a>Co je Asistivní čtečka?

[Atraktivní čtečky](https://www.onenote.com/learningtools) je (včetně) navržené nástroj, který implementuje osvědčených postupů a zlepšit míru porozumění čtení pro nově vznikající čtečky inteligentních algoritmů jazyka, uživatelé, kteří se studijním rozdíly, jako jsou problémy s učením.

Asistivní čtečku můžete využít ve vašich webových aplikacích prostřednictvím sady SDK pro Asistivní čtečku.

## <a name="what-does-immersive-reader-do"></a>Co dělá atraktivní čtečky?

Dokonalé čtečky je navržená tak, aby přístupnější čtení pro všechny uživatele.

* Zobrazí obsah v zobrazení pro minimální čtení

  ![Asistivní čtečka](./media/immersive-reader.png)

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

Začínáme s Asistivní čtečkou:

* Zabývat tím, [rychlý start](./quickstart.md)
* Prozkoumejte [atraktivní čtečky SDK na Githubu](https://github.com/Microsoft/immersive-reader-sdk)
* Čtení [atraktivní čtečky odkaz na sadu SDK](./reference.md)