---
title: Služba Microsoft Translator
titlesuffix: Azure Cognitive Services
description: Integrujte překladatele do svých aplikací, webů, nástrojů a dalších řešení a poskytněte prostředí pro více jazyků.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Překladatel, překlad textu, strojové překlady, překladatelské služby
ms.openlocfilehash: ec76aa7554110b7440eb825f2d5e86ae2da6baa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657718"
---
# <a name="what-is-the-translator-service"></a>Co je služba Translator?

Translator je cloudová služba pro strojový překlad, která je součástí řady rozhraní API pro rozpoznávání [Cognitive Services v Azure](../../index.yml?panel=ai&pivot=products) , která slouží k vytváření inteligentních aplikací. Překladatel se snadno integruje do aplikací, webů, nástrojů a řešení. Umožňuje přidat vícejazyčná prostředí pro uživatele v [90 jazycích a dialektech](./language-support.md) a dá se použít pro překlad textu s jakýmkoli operačním systémem.

Tato dokumentace obsahuje následující typy článků:  

* [**Rychlé starty**](quickstart-translator.md) jsou pokyny Začínáme, které vám pomohou při provádění požadavků na službu.  
* Návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném [**způsobu.**](translator-how-to-signup.md)  
* [**Koncepty**](character-counts.md) poskytují podrobné vysvětlení funkcí a funkcí služby.  
* [**Kurzy**](tutorial-wpf-translation-csharp.md) jsou delší než příručky, které ukazují, jak používat službu jako součást v širších obchodních řešeních.  


## <a name="about-microsoft-translator"></a>Informace o službě Microsoft Translator

Překladatelé řady produktů a služeb Microsoftu a využívají tisíce firem po celém světě v rámci svých aplikací a pracovních postupů.

Překlad řeči, který využívá překladatel, je také k dispozici prostřednictvím [služby Azure Speech Service](../speech-service/index.yml). Kombinuje funkce z Translator Speech API a Custom Speech Service do sjednocené a plně přizpůsobitelné služby. 

## <a name="language-support"></a>Podpora jazyků

Translator poskytuje vícejazyčnou podporu pro překlad textu, převádění, rozpoznávání jazyka a slovníky. Úplný seznam podporovaných jazyků najdete v tématu věnovaném [podpoře jazyků](language-support.md) nebo ho můžete zobrazit programově pomocí rozhraní [REST API](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronový strojový překlad služby Microsoft Translator

Neuronový strojový překlad (NMT) představuje nový standard pro vysoce kvalitní strojové překlady s využitím AI. Nahrazuje starší technologii statistického strojového překladu (SMT), která dosáhla stabilní úrovně kvality přibližně v polovině roku 2010.

NMT poskytuje lepší překlady než SMT nejen z hlediska prostého hodnocení kvality překladu, ale také proto, že překlady budou znít plynuleji a lidštěji. Hlavním důvodem této plynulosti je, že NMT při překladu slov využívá úplný kontext věty. Technologie SMT využívala pouze bezprostřední kontext několika slov před a za každým slovem.

Modely NMT jsou v jádru rozhraní API a nejsou viditelné pro koncové uživatele. Jediným patrným rozdílem je zlepšení kvality překladu, zejména pro jazyky jako čínština, japonština a arabština.

Přečtěte si další informace o [tom, jak NMT funguje](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Vylepšení překladů pomocí vlastního překladatele

 [Vlastní Překladatel](customization.md), rozšíření služby Translator, se dá použít k přizpůsobení systému překladu neuronové a vylepšení překladu konkrétní terminologie a stylu.

Pomocí vlastního překladatele můžete vytvářet překladatelské systémy pro zpracování terminologie používané ve vašem podniku nebo v průmyslu. Přizpůsobený systém překladu můžete snadno integrovat s vašimi stávajícími aplikacemi, pracovními postupy, weby a zařízeními prostřednictvím pravidelného překladatele pomocí parametru Category.

## <a name="next-steps"></a>Další kroky

- [Vytvořte službu Translator](./translator-how-to-signup.md) pro získání přístupových klíčů a koncového bodu.
- Vyzkoušejte si náš [rychlý Start](quickstart-translator.md) pro rychlé volání služby Translator.
- [Reference k rozhraní API](./reference/v3-0-reference.md) poskytuje technickou dokumentaci k rozhraním API.
- [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
