---
title: Co je Custom Speech Service?
titlesuffix: Azure Cognitive Services
description: Custom Speech Service je cloudová služba, která uživatelům umožňuje přizpůsobit modely řeči pro přepis řeči na text.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: overview
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: f90fc40a42806cfb002da2d9943eaa41736df4d7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222739"
---
# <a name="what-is-custom-speech-service"></a>Co je Custom Speech Service?

Custom Speech Service je cloudová služba, která uživatelům poskytuje možnost přizpůsobení modelů řeči pro přepis řeči na text.
Pokud chcete službu Custom Speech Service používat, přejděte na [portál služby Custom Speech Service](https://cris.ai).

Služba Custom Speech Service umožňuje vytvářet jazykové a akustické modely přizpůsobené vaší aplikaci a uživatelům. Po nahrání konkrétních řečových a/nebo textových dat do služby Custom Speech Service můžete vytvořit vlastní modely, které lze použít v kombinaci s existujícími špičkovými modely řeči Microsoftu.

Pokud například přidáváte hlasové interakce do aplikace pro mobilní telefon, tablet nebo počítač, můžete vytvořit vlastní jazykový model, který můžete v kombinaci s akustickým modelem Microsoftu použít k vytvoření koncového bodu převodu řeči na text určeného speciálně pro vaši aplikaci. Jestliže je vaše aplikace určená pro konkrétní prostředí nebo konkrétní populaci uživatelů, můžete pomocí této služby také vytvořit a nasadit vlastní akustický model.


## <a name="how-do-speech-recognition-systems-work"></a>Jak systémy rozpoznávání řeči fungují?
Systémy rozpoznávání řeči se skládají z několika komponent, které vzájemně spolupracují. Mezi dvě nejdůležitější komponenty patří akustický model a jazykový model.

Akustický model je klasifikátor, který označuje krátké zvukové fragmenty jedním z mnoha fonémů, neboli zvukových jednotek, v daném jazyce. Například slovo „speech“ je tvořeno čtyřmi fonémy „s p iy ch“. Tyto klasifikace se provádějí řádově 100krát za sekundu.

Jazykový model je pravděpodobnostní rozdělení přes posloupnosti slov. Jazykový model pomáhá systému rozhodovat mezi posloupnostmi podobně znějících slov na základě pravděpodobnosti samotných posloupností slov. Například posloupnosti „recognize speech“ a „wreck a nice beach“ zní podobně, ale výskyt první z nich je mnohem pravděpodobnější, takže jí bude jazykovým modelem přiřazeno vyšší skóre.

Akustické i jazykové modely jsou statistické modely, které se učí z trénovacích dat. Ve výsledku pak fungují nejlépe, pokud se řeč, kterou rozpoznávají v aplikacích, podobá datům zpracovaným během tréninku. Akustické a jazykové modely v modulu Microsoftu pro převod řeči na text byly trénovány na ohromné kolekci řeči a textu a ve většině běžných situací, ke kterým patří interakce s Cortanou na smartphonu, tabletu nebo v počítači, hlasové vyhledávání na webu nebo diktování SMS zpráv přátelům, poskytují špičkový výkon.

## <a name="why-use-the-custom-speech-service"></a>Proč službu Custom Speech Service používat?
Přestože modul Microsoftu pro převod řeči na text patří ke světové špičce, je zaměřený na výše popsané situace. Pokud ale očekáváte, že hlasové dotazy ve vaší aplikaci budou obsahovat určité slovníkové položky, jako jsou názvy produktů nebo žargon, který se v běžné řeči vyskytuje zřídka, je pravděpodobné, že přizpůsobením jazykového modelu můžete výkon zlepšit.

Pokud například vytváříte aplikaci pro hlasové vyhledávání na webu MSDN, je pravděpodobné, že se termíny jako „object-oriented“, „namespace“ nebo „dot net“ budou vyskytovat častěji než v klasických hlasových aplikacích. Přizpůsobení jazykového modelu umožní systému se to naučit.

## <a name="next-steps"></a>Další kroky

Další informace o používání služby Custom Speech Service najdete na [portálu služby Custom Speech Service] (https://cris.ai).

* [Začínáme](cognitive-services-custom-speech-get-started.md)
* [Nejčastější dotazy](cognitive-services-custom-speech-faq.md)
* [Glosář](cognitive-services-custom-speech-glossary.md)
