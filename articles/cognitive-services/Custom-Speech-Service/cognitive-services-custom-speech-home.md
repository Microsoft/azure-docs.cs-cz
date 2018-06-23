---
title: Vlastní přehled řeči služby v Azure | Microsoft Docs
description: Vlastní rozpoznávání řeči služby je Cloudová služba, která umožní uživatelům přizpůsobit rozpoznávání řeči modely pro přepis řeči na text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: a6139283a555f8f97371c02f9d1f3d53dc6f15d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342871"
---
# <a name="what-is-custom-speech-service"></a>Co je služba rozpoznávání řeči vlastní?

Vlastní rozpoznávání řeči Service je Cloudová služba, která uživatelům poskytuje schopnost přizpůsobit rozpoznávání řeči modely pro přepis řeči na Text.
Používat službu vlastní rozpoznávání řeči, naleznete [portál služby řeči vlastní](https://cris.ai).

Vlastní rozpoznávání řeči služby umožňuje vytvářet vlastní jazyk modely a akustickými modely, které jsou pro aplikaci a uživatele. Tím, že nahrajete konkrétní řeči nebo textová data ke službě vlastní rozpoznávání řeči, můžete vytvořit vlastní modely, které lze použít ve spojení s existující modely stavu techniky řeči společnosti Microsoft.

Například pokud přidáváte hlasové interakce na mobilní telefon, tablet nebo počítači aplikace, můžete vytvořit vlastní jazyk model, který je možné kombinovat s akustickými modelu společnosti Microsoft vytvořit koncový bod řeči na text, který je určený pro vaši aplikaci. Pokud vaše aplikace je určen k použití v konkrétním prostředí nebo naplnění určitého uživatele, můžete také vytvořit a nasadit vlastní akustickými modelu s touto službou.


## <a name="how-do-speech-recognition-systems-work"></a>Jak fungují systémy rozpoznávání řeči?
Systémy rozpoznávání řeči se skládají z několika komponent, které vzájemně spolupracují. Dva nejdůležitější součásti jsou akustickými modelu a modelu jazyka.

Akustickými model je třídění, které označuje krátké fragmenty zvuk do jednoho čísla fonémy nebo zvukové jednotky, v daném jazyce. Například slovo "řeči" se skládá ze čtyř fonémy "s p iy ch". Tyto klasifikace se provádějí řádově stokrát za sekundu.

Jazykový model je pravděpodobnostní rozdělení přes posloupnosti slov. Jazykový model pomáhá systému zvolit takovou posloupnost slov, která zní podobně, a to na základě pravděpodobnosti jednotlivých posloupností slov. Například posloupnosti „recognize speech“ a „wreck a nice beach“ znějí podobně, ale výskyt první z nich je mnohem pravděpodobnější, takže jí bude jazykovým modelem přiřazené vyšší ohodnocení.

Acoustic i jazyk modely jsou statistické modely vytvořeným ze Cvičná data. V důsledku toho provádějí nejlépe při rozpoznávání řeči, na které narazí při použití v aplikacích je podobná data zaznamenali během cvičení. Modely acoustic a jazyk v modulu Microsoft řeči na Text byl trénink na kolekci značné řeč a textu a poskytují výkon stavu techniky pro nejčastější scénáře použití, například interakci s Cortana na vaše čipové Telefon, tablet nebo počítač, vyhledávání na webu pomocí hlasového nebo diktování textové zprávy na přítele.

## <a name="why-use-the-custom-speech-service"></a>Proč používat službu řeči vlastní?
Sice špičkových modul Microsoft řeči na Text, je určen k scénáře popsané výše. Ale pokud očekáváte, že hlas dotazy k aplikaci tak, aby obsahovala určité termínů položky, jako jsou názvy produktů nebo žargonu zabezpečení dochází jen zřídka v typické rozpoznávání řeči, je pravděpodobné, že můžete získat lepší výkon přizpůsobením jazyka modelu.

Pokud například vytváříte aplikaci umožňující hlasové dotazy na web MSDN, je pravděpodobné, že termíny jako „object-oriented“, „namespace“ nebo „dot net“ se budou vyskytovat častěji než v klasických hlasových aplikacích. Přizpůsobení jazykového modelu umožní systému se to naučit.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak používat službu vlastní rozpoznávání řeči, najdete v tématu [vlastní řeči portálu služby] (https://cris.ai).

* [Začínáme](cognitive-services-custom-speech-get-started.md)
* [Nejčastější dotazy](cognitive-services-custom-speech-faq.md)
* [Glosář](cognitive-services-custom-speech-glossary.md)
