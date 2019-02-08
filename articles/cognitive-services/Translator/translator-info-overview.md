---
title: Co je Translator Text API? – Translator Text API
titlesuffix: Azure Cognitive Services
description: Integrací služby Translator Text API do vašich aplikací, webů, nástrojů a dalších řešení můžete poskytnout vícejazyčná uživatelská prostředí.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: df0f1570b4f876cbfb542a5571c8bec7e80e6895
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857713"
---
# <a name="what-is-translator-text-api"></a>Co je Translator Text API?

Službu Translator Text API můžete snadno integrovat do svých aplikací, webů, nástrojů a služeb. Umožní vám přidat vícejazyčná uživatelská prostředí ve [více než 60 jazycích](languages.md) a můžete ji použít k překladu jazyka z textu do textu na jakékoli hardwarové platformě s jakýmkoli operačním systémem.

Translator Text API je součástí kolekce [rozhraní API služeb Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) obsahující algoritmy strojového učení a umělé inteligence v cloudu a je připravená k použití ve vašich vývojových projektech.

## <a name="about-microsoft-translator"></a>Informace o službě Microsoft Translator

Microsoft Translator je cloudová služba pro strojový překlad. Hlavní službou je Translator Text API, která se využívá v řadě produktů a služeb Microsoftu a v aplikacích a pracovních postupech tisíců firem po celém světě, kde umožňuje šíření obsahu k cílové skupině po celém světě.

Překlad řeči s využitím služby Translator Text API je k dispozici také prostřednictvím [služby Microsoft Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Tato jednotná a plně přizpůsobitelná služba v sobě kombinuje funkce ze služeb Translator Speech API, Zpracování řeči Bingu a Custom Speech (Preview). Služba Speech nahrazuje službu Translator Speech API, která se 15. října 2019 vyřadí z provozu.

## <a name="language-support"></a>Podpora jazyků

Microsoft Translator nabízí podporu více jazyků pro překlad, transkripci, rozpoznávání jazyka a slovníky. Úplný seznam podporovaných jazyků najdete v tématu věnovaném [podpoře jazyků](language-support.md) nebo ho můžete zobrazit programově pomocí rozhraní [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="language-customization"></a>Přizpůsobení jazyka

Ve spojení se službou Translator Text API je možné použít rozšíření Custom Translator základní služby Microsoft Translator, které vám pomůže přizpůsobit neuronový překladový systém a zlepšit překlad pro vaši specifickou terminologii a styl.

S rozšířením Custom Translator můžete vytvářet překladové systémy, které si poradí s terminologií používanou ve vaší firmě nebo oboru. Váš přizpůsobený překladový systém se pak snadno integruje do stávajících aplikací, pracovních postupů a webů na různých typech zařízení prostřednictvím normální služby Microsoft Translator Text API s použitím parametru kategorie.

Další informace o [přizpůsobení jazyka](customization.md)

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronový strojový překlad služby Microsoft Translator

Neuronový strojový překlad (NMT) představuje nový standard pro vysoce kvalitní strojové překlady s využitím AI. Nahrazuje starší technologii statistického strojového překladu (SMT), která dosáhla stabilní úrovně kvality přibližně v polovině roku 2010.

NMT poskytuje lepší překlady než SMT nejen z hlediska prostého hodnocení kvality překladu, ale také proto, že překlady budou znít plynuleji a lidštěji. Hlavním důvodem této plynulosti je, že NMT při překladu slov využívá úplný kontext věty. Technologie SMT využívala pouze bezprostřední kontext několika slov před a za každým slovem.

Modely NMT jsou v jádru rozhraní API a nejsou viditelné pro koncové uživatele. Jediným patrným rozdílem je zlepšení kvality překladu, zejména pro jazyky jako čínština, japonština a arabština.

Další informace o tom, [jak NMT funguje](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Další postup

- Přečtěte si [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Zaregistrujte se](translator-text-how-to-signup.md) a získejte přístupový klíč.

- [Rychlý start](quickstarts/csharp.md) obsahuje vysvětlení volání rozhraní REST API napsaných v jazyce C#. Zjistěte, jak překládat text z jednoho jazyka do jiného s minimem kódu.

- [Referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) obsahuje technickou dokumentaci k rozhraním API.

## <a name="see-also"></a>Další informace najdete v tématech

- [Stránka dokumentace ke službám Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informace o řešení a cenách](https://www.microsoft.com/en-us/translator/default.aspx)
