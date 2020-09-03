---
title: Co je Translator? – Překladatel
titlesuffix: Azure Cognitive Services
description: Integrujte překladatele do svých aplikací, webů, nástrojů a dalších řešení a poskytněte prostředí pro více jazyků.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 206e1bc86404fa41b84647c50482dbe63301c0af
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89425955"
---
# <a name="what-is-translator"></a>Co je Translator?

Azure Cognitive Services Translator je cloudová služba pro strojový překlad, která je součástí skupiny [Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) , která je založená na rozhraních API pro rozpoznávání a vytváření inteligentních aplikací. Překladatel se snadno integruje do aplikací, webů, nástrojů a řešení. Umožňuje přidat vícejazyčné uživatelské prostředí ve [více než 70 jazycích](languages.md)a můžete ho použít na libovolné hardwarové platformě s jakýmkoli operačním systémem pro převod textu na text.

## <a name="about-microsoft-translator"></a>Informace o službě Microsoft Translator

Azure Cognitive Services Translator je cloudová služba pro překlad počítačů. Translator je řada produktů a služeb společnosti Microsoft a používá se v nich v rámci svých aplikací a pracovních postupů tisíce firem po celém světě, což umožňuje, aby jejich obsah dosáhl globální cílové skupiny.

Překlad řeči, který využívá překladatel, je také k dispozici prostřednictvím [služby Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Kombinuje funkce z Translator Speech API a Custom Speech Service do sjednocené a plně přizpůsobitelné služby. 

## <a name="language-support"></a>Podpora jazyků

Microsoft Translator nabízí podporu více jazyků pro překlad, transkripci, rozpoznávání jazyka a slovníky. Úplný seznam podporovaných jazyků najdete v tématu věnovaném [podpoře jazyků](language-support.md) nebo ho můžete zobrazit programově pomocí rozhraní [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronový strojový překlad služby Microsoft Translator

Neuronový strojový překlad (NMT) představuje nový standard pro vysoce kvalitní strojové překlady s využitím AI. Nahrazuje starší technologii statistického strojového překladu (SMT), která dosáhla stabilní úrovně kvality přibližně v polovině roku 2010.

NMT poskytuje lepší překlady než SMT nejen z hlediska prostého hodnocení kvality překladu, ale také proto, že překlady budou znít plynuleji a lidštěji. Hlavním důvodem této plynulosti je, že NMT při překladu slov využívá úplný kontext věty. Technologie SMT využívala pouze bezprostřední kontext několika slov před a za každým slovem.

Modely NMT jsou v jádru rozhraní API a nejsou viditelné pro koncové uživatele. Jediným patrným rozdílem je zlepšení kvality překladu, zejména pro jazyky jako čínština, japonština a arabština.

Další informace o tom, [jak NMT funguje](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Přizpůsobení jazyka

Rozšíření služby Translator, vlastní Překladatel se dá použít společně s překladatelem, který vám usnadní přizpůsobení neuronové systému překladu a vylepšení překladu konkrétní terminologie a stylu.

S rozšířením Custom Translator můžete vytvářet překladové systémy, které si poradí s terminologií používanou ve vaší firmě nebo oboru. Přizpůsobený systém překladu se pak snadno integruje do vašich stávajících aplikací, pracovních postupů a webů napříč různými typy zařízení prostřednictvím pravidelného překladatele pomocí parametru Category (kategorie).

Další informace o [přizpůsobení jazyka](customization.md)

## <a name="next-steps"></a>Další kroky

- [Zaregistrujte](translator-text-how-to-signup.md) si přístupový klíč.
- [Reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) poskytuje technickou dokumentaci k rozhraním API.
- [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
