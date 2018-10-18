---
title: Co je Translator Text API?
titlesuffix: Azure Cognitive Services
description: Integrací služby Translator Text API do vašich aplikací, webů, nástrojů a dalších řešení můžete poskytnout vícejazyčná uživatelská prostředí.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: c0004960b7a234d374ec0ae1bdc2f6576b197705
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385020"
---
# <a name="what-is-translator-text-api"></a>Co je Translator Text API?

Službu Translator Text API můžete bezproblémově integrovat do svých aplikací, webů, nástrojů a dalších řešení a poskytnout vícejazyčná uživatelská prostředí ve [více než 60 jazycích](languages.md). Můžete ji použít na jakékoli hardwarové platformě a s jakýmkoli operačním systémem, kde bude provádět překlad jazyka z textu do textu.

Translator Text API je součástí [kolekce Azure Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=ai) obsahující algoritmy strojového učení a umělé inteligence v cloudu a je připravená k použití ve vašich vývojových projektech.

## <a name="about-microsoft-translator"></a>Informace o službě Microsoft Translator

Microsoft Translator je cloudová služba pro strojový překlad. Jádrem této služby jsou služby Translator Text API a [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation), které se využívají v různých produktech a službách Microsoftu a v aplikacích a pracovních postupech tisíců firem po celém světě, kde umožňují šíření obsahu k cílové skupině po celém světě.

Překlad řeči je k dispozici také prostřednictvím služby [Cognitive Services Speech Preview](https://docs.microsoft.com/azure/cognitive-services/speech-service/), která kombinuje stávající služby Translator Speech API, Zpracování řeči Bingu a Custom Speech (Preview) do jednotné a plně přizpůsobitelné služby.  

Další informace o [službě Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="language-customization"></a>Přizpůsobení jazyka

Ve spojení se službou Translator Text API je možné použít rozšíření Custom Translator základní služby Microsoft Translator, které vám pomůže přizpůsobit neuronový překladový systém a zlepšit překlad pro vaši specifickou terminologii a styl.

S rozšířením Custom Translator můžete vytvářet překladové systémy, které si poradí s terminologií používanou ve vaší firmě nebo oboru. Váš přizpůsobený překladový systém se pak snadno integruje do stávajících aplikací, pracovních postupů a webů na různých typech zařízení prostřednictvím normální služby Microsoft Translator Text API s použitím parametru kategorie. 

Další informace o [přizpůsobení jazyka](customization.md)

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronový strojový překlad služby Microsoft Translator

Neuronový strojový překlad (NMT) představuje nový standard pro vysoce kvalitní strojové překlady s využitím AI. Nahrazuje starší technologii statistického strojového překladu (SMT), která dosáhla stabilní úrovně kvality přibližně v polovině roku 2010.

NMT poskytuje lepší překlady než SMT nejen z hlediska prostého hodnocení kvality překladu, ale také proto, že překlady budou znít plynuleji a lidštěji. Hlavním důvodem této plynulosti je, že NMT při překladu slov využívá úplný kontext věty. Technologie SMT využívala pouze bezprostřední kontext několika slov před a za každým slovem.

Modely NMT jsou v jádru rozhraní API a nejsou viditelné pro koncové uživatele. Jediným patrným rozdílem je zlepšení kvality překladu, zejména pro jazyky jako čínština, japonština a arabština. 

Další informace o tom, [jak NMT funguje](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Další kroky

- Přečtěte si [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Zaregistrujte se](translator-text-how-to-signup.md) a získejte přístupový klíč.

- [Rychlý start](quickstarts/csharp.md) obsahuje vysvětlení volání rozhraní REST API napsaných v jazyce C#. Zjistěte, jak překládat text z jednoho jazyka do jiného s minimem kódu.

- [Referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) obsahuje technickou dokumentaci k rozhraním API.

## <a name="see-also"></a>Viz také

- [Stránka dokumentace ke službám Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informace o řešení a cenách](https://www.microsoft.com/en-us/translator/default.aspx)
