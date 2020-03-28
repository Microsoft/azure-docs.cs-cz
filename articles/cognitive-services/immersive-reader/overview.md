---
title: Co je rozhraní API pro immersive reader?
titleSuffix: Azure Cognitive Services
description: Rozhraní Immersive Reader API je nástroj, který lze použít k ubytování lidí s rozdíly v učení nebo pomoci novým čtenářům a studentům jazyků.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052379"
---
# <a name="what-is-immersive-reader"></a>Co je Asistivní čtečka?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

[Immersive Reader](https://www.onenote.com/learningtools) je inkluzivně navržený nástroj, který implementuje osvědčené techniky pro zlepšení čtení porozumění pro začínající čtenáře, studenty jazyků a lidi s rozdíly v učení, jako je dyslexie.

Asistivní čtečku můžete využít ve vašich webových aplikacích prostřednictvím sady SDK pro Asistivní čtečku.

## <a name="what-does-immersive-reader-do"></a>Co dělá Immersive Reader?

Pohlcující čtečka je navržena tak, aby čtení bylo přístupnější pro každého.

* Zobrazuje obsah v minimálním zobrazení pro čtení.

  ![Asistivní čtečka](./media/immersive-reader.png)

* Zobrazí obrázky běžně používaných slov.

  ![Slovník obrázků](./media/picture-dictionary.png)

* Zvýrazňují ta hřbeta, slovesa, přídavná jména a příslovce

  ![Části řeči](./media/parts-of-speech.png)

* Čte vám obsah nahlas

  ![Číst nahlas](./media/read-aloud.png)

* Převede obsah do jiného jazyka.

  ![Překlad](./media/translation.png)

* Rozdělí slova na slabiky

  ![Syllabifikace](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak funguje immersive Reader?

Immersive Reader je samostatná webová aplikace, která se při vyvolání pomocí sady JavaScript SDK pro immersive Reader zobrazí v horní části vaší stávající webové aplikace prostřednictvím sady `iframe`. Při volání rozhraní API ke spuštění immersive Reader, zadáte obsah, který chcete zobrazit v immersive Reader. Naše sada SDK zpracovává vytváření a `iframe` styling a komunikaci s back-endovou službou Immersive Reader, která zpracovává obsah částí řeči, textu na řeč, překladu a tak dále.

## <a name="next-steps"></a>Další kroky

Začínáme s Asistivní čtečkou:

* Skočit do [rychlého startu](./quickstart.md)
* Prozkoumejte sadku [Immersive Reader SDK na GitHubu](https://github.com/microsoft/immersive-reader-sdk)
* Přečtěte si referenční příručku sady [Immersive Reader SDK](./reference.md)
