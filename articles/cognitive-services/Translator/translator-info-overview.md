---
title: Služba Microsoft Translator
titlesuffix: Azure Cognitive Services
description: Integrujte překladatele do svých aplikací, webů, nástrojů a dalších řešení a poskytněte prostředí pro více jazyků.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: swmachan
ms.custom: cog-serv-seo-aug-2020
keywords: Překladatel, překlad textu, strojové překlady, překladatelské služby
ms.openlocfilehash: 32ae7f75c1b953e8af7dfef83c1971c2f78b0b62
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530331"
---
# <a name="what-is-the-translator-service"></a>Co je služba Translator?

Translator je cloudová služba pro strojový překlad, která je součástí řady rozhraní API pro rozpoznávání [Cognitive Services v Azure](https://docs.microsoft.com/azure/?pivot=products&panel=ai) , která slouží k vytváření inteligentních aplikací. Překladatel se snadno integruje do aplikací, webů, nástrojů a řešení. Umožňuje přidat vícejazyčná uživatelská prostředí ve [více než 70 jazycích](languages.md)a můžete ji použít na libovolné hardwarové platformě s jakýmkoli operačním systémem pro překlad textu.

## <a name="about-microsoft-translator"></a>Informace o službě Microsoft Translator

Translator je řada produktů a služeb společnosti Microsoft a používá se v nich v rámci svých aplikací a pracovních postupů tisíce firem po celém světě, což umožňuje, aby jejich obsah dosáhl globální cílové skupiny.

Překlad řeči, který využívá překladatel, je také k dispozici prostřednictvím [služby Azure Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Kombinuje funkce z Translator Speech API a Custom Speech Service do sjednocené a plně přizpůsobitelné služby. 

## <a name="language-support"></a>Podpora jazyků

Translator poskytuje vícejazyčnou podporu pro překlad textu, převádění, rozpoznávání jazyka a slovníky. Úplný seznam podporovaných jazyků najdete v tématu věnovaném [podpoře jazyků](language-support.md) nebo ho můžete zobrazit programově pomocí rozhraní [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronový strojový překlad služby Microsoft Translator

Neuronový strojový překlad (NMT) představuje nový standard pro vysoce kvalitní strojové překlady s využitím AI. Nahrazuje starší technologii statistického strojového překladu (SMT), která dosáhla stabilní úrovně kvality přibližně v polovině roku 2010.

NMT poskytuje lepší překlady než SMT nejen z hlediska prostého hodnocení kvality překladu, ale také proto, že překlady budou znít plynuleji a lidštěji. Hlavním důvodem této plynulosti je, že NMT při překladu slov využívá úplný kontext věty. Technologie SMT využívala pouze bezprostřední kontext několika slov před a za každým slovem.

Modely NMT jsou v jádru rozhraní API a nejsou viditelné pro koncové uživatele. Jediným patrným rozdílem je zlepšení kvality překladu, zejména pro jazyky jako čínština, japonština a arabština.

Přečtěte si další informace o [tom, jak NMT funguje](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Vylepšení překladů pomocí vlastního překladatele

Rozšíření služby Translator, vlastní Překladatel se dá použít společně s překladatelem, který vám usnadní přizpůsobení neuronové systému překladu a vylepšení překladu konkrétní terminologie a stylu.

S rozšířením Custom Translator můžete vytvářet překladové systémy, které si poradí s terminologií používanou ve vaší firmě nebo oboru. Přizpůsobený systém překladu se pak snadno integruje do vašich stávajících aplikací, pracovních postupů a webů napříč různými typy zařízení prostřednictvím pravidelného překladatele pomocí parametru Category (kategorie).

Přečtěte si další informace o [vlastním překladateli](customization.md).

## <a name="next-steps"></a>Další kroky

- [Zaregistrujte](translator-text-how-to-signup.md) si přístupový klíč.
- Vyzkoušejte si náš [rychlý Start](quickstart-translator.md) pro rychlé volání služby Translator.
- [Reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) poskytuje technickou dokumentaci k rozhraním API.
- [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
