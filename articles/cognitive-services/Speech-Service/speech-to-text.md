---
title: Přehled převodu řeči na text – služba Speech
titleSuffix: Azure Cognitive Services
description: Software pro převod řeči na text umožňuje přepis zvukových streamů v reálném čase na text. Tyto textové vstupy můžou aplikace, nástroje a zařízení využívat, zobrazovat a provádět s nimi akce. Tento článek představuje přehled výhod a možností služby Speech-to-text.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: Převod řeči na text, převod řeči na textový software
ms.openlocfilehash: 3450d39729096bfc3077f51e2069f8f102e571a5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449388"
---
# <a name="what-is-speech-to-text"></a>Co je převod řeči na text?

V tomto přehledu se dozvíte o výhodách a funkcích služby Speech-to-text.
Převod řeči na text, označovaný také jako rozpoznávání řeči, umožňuje přepis zvukových streamů v reálném čase na text. Vaše aplikace, nástroje nebo zařízení můžou používat, zobrazovat a provádět na tomto textu akce jako vstup příkazu. Tato služba využívá stejnou technologii rozpoznávání, kterou Microsoft používá pro Cortana a produkty Office. Bez problémů funguje s nabídkami služeb pro <a href="./speech-translation.md" target="_blank">převod a převod </a> <a href="./text-to-speech.md" target="_blank">textu na řeč </a> . Úplný seznam dostupných jazyků pro převod řeči na text najdete v tématu [podporované jazyky](language-support.md#speech-to-text).

Služba převod řeči na text standardně používá univerzální jazykový model. Tento model byl vyškolený pomocí dat vlastněných společností Microsoft a nasazený v cloudu. Je ideální pro scénáře konverzace a diktování. Při použití převodu řeči na text pro rozpoznávání a přepis v jedinečném prostředí můžete vytvořit a naučit vlastní modely akustického, jazyka a výslovnosti. Přizpůsobení je užitečné pro řešení hlučného hluku nebo slovníku specifického pro konkrétní odvětví.

Tato dokumentace obsahuje následující typy článků:

* **Rychlé starty** jsou pokyny Začínáme, které vám pomohou při provádění požadavků na službu.
* Návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném **způsobu.**
* **Koncepty** poskytují podrobné vysvětlení funkcí a funkcí služby.
* **Kurzy** jsou delší než příručky, které ukazují, jak používat službu jako součást v širších obchodních řešeních.

> [!NOTE]
> Zpracování řeči Bingu byl vyvyřazen z 15. října 2019. Pokud vaše aplikace, nástroje nebo produkty používají rozhraní API Zpracování řeči Bingu, vytvořili jsme příručky, které vám pomůžou s migrací na službu Speech.
> - [Migrace z Zpracování řeči Bingu do služby pro rozpoznávání řeči](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Začínáme

Projděte si [rychlý Start](get-started-speech-to-text.md) a začněte s převodem řeči na text. Služba je k dispozici prostřednictvím [sady Speech SDK](speech-sdk.md), [REST API](rest-speech-to-text.md#pronunciation-assessment-parameters)a rozhraní příkazového [řádku pro rozpoznávání řeči](spx-overview.md).

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro sadu Speech SDK je k dispozici na GitHubu. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely.

- [Ukázky řeči na text (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky hodnocení výslovnosti (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Přizpůsobení

Kromě standardního modelu služby Speech můžete vytvořit vlastní modely. Přizpůsobení pomáhá překonat překážky rozpoznávání řeči, jako je například mluvený styl, slovník a šum na pozadí, viz [Custom Speech](./custom-speech-overview.md). Možnosti vlastního nastavení se liší podle jazyka nebo národního prostředí, viz [podporované jazyky](./language-support.md) pro ověření podpory.

## <a name="batch-transcription"></a>Dávkový přepis

Batch přepis je sada REST API operací, které umožňují přepisovat velké množství zvuků v úložišti. Na zvukové soubory můžete odkazovat pomocí identifikátoru URI sdíleného přístupového podpisu (SAS) a asynchronně přijímat výsledky přepisu. Další informace o tom, jak používat rozhraní API služby Batch pro přepis, najdete v tématu [How to](batch-transcription.md) .

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Další kroky

- [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
- [Získat sadu Speech SDK](speech-sdk.md)