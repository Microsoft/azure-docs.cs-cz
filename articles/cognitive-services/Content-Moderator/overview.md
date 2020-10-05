---
title: Co je Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Dozvíte se, jak s použitím Content Moderatoru sledovat, označovat příznaky, vyhodnocovat a filtrovat nevhodný materiál v uživatelem generovaném obsahu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: moderátor obsahu, moderátor obsahu Azure, moderátor online, software pro filtrování obsahu, služba pro moderování obsahu, Moderování obsahu
ms.openlocfilehash: aa1f4c1fab9b3d8a1b5db0e965f1608e694646b2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91596850"
---
# <a name="what-is-azure-content-moderator"></a>Co je Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator je služba AI, která umožňuje zpracovávat obsah, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Obsahuje službu pro moderování obsahu s podporou AI, která prohledává text, obrázky a videa a automaticky používá příznaky obsahu a také nástroj pro kontrolu online prostředí moderátora pro tým lidských kontrolorů.

Do své aplikace můžete chtít sestavit software pro filtrování obsahu, aby bylo možné dodržovat předpisy nebo zachovat zamýšlené prostředí pro vaše uživatele.

## <a name="where-its-used"></a>Kde se používá

Následuje několik scénářů, ve kterých může vývojář softwaru nebo tým vyžadovat službu pro moderování obsahu:

- Online tržiště, které jsou moderované katalogy produktů a další uživatelem generovaný obsah.
- Herní společnosti, které středně vytvářené herní artefakty a konverzační místnosti.
- Systémy zasílání zpráv pro sociální sítě, které přidávají jejich uživatelé jako střední obrázky, text a videa.
- Podnikové mediální společnosti, které implementují centralizované moderování pro svůj obsah.
- K-12 poskytovatelé řešení pro vzdělávání odfiltrují obsah, který je nevhodný pro studenty a pedagogy.

> [!IMPORTANT]
> Content Moderator nelze použít k detekci neplatných imagí pro zneužití. Kvalifikovaní organizace ale můžou použít [cloudovou službu PhotoDNA](https://www.microsoft.com/photodna "Cloudová služba Microsoft PhotoDNA") na obrazovku pro tento typ obsahu.

## <a name="what-it-includes"></a>Co zahrnuje

Služba Content Moderator se skládá z několika rozhraní API webových služeb, která jsou k dispozici prostřednictvím volání REST i sady .NET SDK. Zahrnuje také nástroj pro revize, který umožňuje lidským kontrolorům pomoci služby a zdokonalit nebo vyladit funkci moderování.

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Služba Content Moderator zahrnuje rozhraní API pro moderování, které kontroluje obsah pro materiál, který je potenciálně nevhodný nebo nevhodný.

![Blokový diagram pro rozhraní API pro moderování Content Moderator](images/content-moderator-mod-api.png)

Následující tabulka popisuje různé typy rozhraní API pro moderování.

| Skupina rozhraní API | Popis |
| ------ | ----------- |
|[**Moderování textu**](text-moderation-api.md)| Kontroluje text pro urážlivý obsah, sexuální explicitní nebo sugestivní obsah, vulgární výrazy a osobní údaje.|
|[**Seznamy vlastních výrazů**](try-terms-list-api.md)| Vyhledá text na základě vlastního seznamu podmínek spolu s vestavěnými podmínkami. Pomocí vlastních seznamů můžete blokovat nebo povolovat obsah podle vlastních zásad obsahu.|  
|[**Moderování obrázků**](image-moderation-api.md)| Vyhledává na obrázcích obsah pro dospělé nebo nevhodný obsah, rozpoznává text na obrázcích pomocí funkce optického rozpoznávání znaků (OCR) a rozpoznává tváře.|
|[**Seznamy vlastních obrázků**](try-image-list-api.md)| Vyhledává na obrázcích obrázky z vlastního seznamu. Pomocí seznamů vlastních obrázků můžete vyfiltrovat instance běžně se opakujícího obsahu, který nechcete znovu klasifikovat.|
|[**Moderování videa**](video-moderation-api.md)| Vyhledává ve videích nevhodný obsah nebo obsah pro dospělé a vrací pro uvedený obsah časové značky.|

## <a name="review-apis"></a>Recenze rozhraní API

Rozhraní API pro kontrolu vám umožňují integrovat kanál moderování s lidmi revidujícími. K vytváření a automatizaci pracovních postupů pomocí [Nástroje pro kontrolu](#review-tool) (níže) použijte [úlohy](review-api.md#jobs), [recenze](review-api.md#reviews)a operace [pracovního postupu](review-api.md#workflows) .

> [!NOTE]
> Rozhraní API pracovního postupu ještě není dostupné v sadě .NET SDK, ale dá se použít s koncovým bodem REST.

![Blokový diagram pro rozhraní API pro kontrolu Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Nástroj pro revize

Služba Content Moderator také zahrnuje webový [Nástroj pro revizi](Review-Tool-User-Guide/human-in-the-loop.md), který hostuje revize obsahu pro lidské moderování ke zpracování. Lidské vstupy tuto službu nevlakují, ale kombinovaná práce týmů služby a lidských revizí umožňuje vývojářům přeškrtnout správné rovnováhu mezi efektivitou a přesností. Nástroj pro kontrolu také poskytuje uživatelsky přívětivý front-end pro několik Content Moderatorch prostředků.

![Domovská stránka nástroje Content Moderator Revize](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Content Moderator službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat Content Moderator na webovém portálu, postupujte podle [Content moderator vyzkoušet na webu](quick-start.md). Nebo dokončete průvodce [rychlým startem klientské knihovny](client-libraries.md) , který implementuje základní scénáře v kódu.