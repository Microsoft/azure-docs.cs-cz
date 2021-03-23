---
title: Co je moderní čtečka?
titleSuffix: Azure Cognitive Services
description: Moderní čtečka je nástroj, který je navržený tak, aby pomáhal lidem s rozdíly v učení, nebo pomáhá novým čtenářům a jazykovým nástrojům s porozuměním při čtení.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: čtenáři, nástroje pro jazyky, zobrazované obrázky, vylepšení čtení, čtení obsahu, překlad
ms.openlocfilehash: 1911d3ce62434f1dc24966798e9ace259ae072bc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801566"
---
# <a name="what-is-immersive-reader"></a>Co je Asistivní čtečka?

[Moderní čtečka](https://www.onenote.com/learningtools) je často navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení pro nové čtenáře, jazyky jazyků a lidi s rozdíly v učení, jako je dyslexia. Pomocí klientské knihovny pro moderní čtečku můžete využívat stejnou technologii používanou v Microsoft Wordu a Microsoftu jednu poznámku ke zlepšení svých webových aplikací. 

Tato dokumentace obsahuje následující typy článků:  

* **[Rychlé starty](quickstarts/client-libraries.md)** jsou pokyny Začínáme, které vám pomohou při provádění požadavků na službu.
* Návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném **[způsobu.](how-to-create-immersive-reader.md)**

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>Použití moderního čtecího zařízení ke zlepšení usnadnění čtení 

Moderní čtečka je navržená tak, aby pro každého usnadnila čtení a přístup. Pojďme se podívat na několik základních funkcí moderního čtečky.

### <a name="isolate-content-for-improved-readability"></a>Izolace obsahu pro lepší čitelnost

Moderní čtečka izoluje obsah, aby se zlepšila čitelnost. 

  ![Izolace obsahu pro zlepšení čitelnosti pomocí moderního čtecího zařízení](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Zobrazit obrázky pro běžná slova

Pro běžně používané výrazy zobrazí moderní čtečka obrázek.

  ![Slovník obrázků s moderní čtečkou](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>Zvýraznění částí řeči

Moderní čtečka se dá využít k tomu, aby usnadnila pochopení částí řeči a gramatiky tím, že zvýrazňuje slovesa, podstatná jména, podstatná jména a další informace.

  ![Zobrazení částí hlasu pomocí moderního čtecího zařízení](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Číst obsah nahlas

Syntéza řeči (nebo převod textu na řeč) se vloženými do moderní čtečky, která umožňuje čtenářům vybrat text, který má být čten. 

  ![Hlasité čtení textu pomocí moderního čtecího zařízení](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Překlad obsahu v reálném čase

Moderní čtečka dokáže překládat text do mnoha jazyků v reálném čase. To je užitečné pro zlepšení srozumitelnosti čtenářů, kteří se učí nový jazyk.

  ![Překlad textu pomocí moderního čtecího zařízení](./media/translation.png)

### <a name="split-words-into-syllables"></a>Rozdělit slova na slabiky

Pomocí moderního čtecího zařízení můžete rozdělit slova na slabiky, aby se zlepšila čitelnost nebo aby se vyzněla nová slova.

  ![Rozdělení slov na slabiky pomocí moderního čtecího zařízení](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak funguje moderní čtečka?

Moderní čtečka je samostatná webová aplikace. Při vyvolání pomocí klientské knihovny pro moderní čtečku se zobrazí na vaší stávající webové aplikaci v `iframe` . Když vaše aplikace WEP volá službu pro moderní čtení, určíte obsah pro zobrazení čtecího modulu. Klientská knihovna pro moderní čtečku zpracovává vytváření a stylování `iframe` a komunikaci s back-end službou pro moderní čtečku. Služba moderního čtecího zařízení zpracovává obsah pro části řeči, převod textu na řeč, překlad a další funkce.

## <a name="get-started-with-immersive-reader"></a>Začínáme s moderní čtečkou

Klientská knihovna pro moderní čtení je k dispozici v jazycích C#, JavaScript, Java (Android), Kotlin (Android) a SWIFT (iOS). Při zahájení práce využijte tyto možnosti:

* [Rychlý Start: použití klientské knihovny pro moderní čtenáře](quickstarts/client-libraries.md)
