---
title: Co je Custom Translator?
titleSuffix: Azure Cognitive Services
description: Vlastní Překladatel nabízí podobné možnosti, které centrum Microsoftu používá pro statistickou strojový překlad (SMT), ale výhradně pro systémy NMT (neuronové Machine Translation).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d78767474150bc9571b25fe1f26135d6f41d1f20
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961230"
---
# <a name="what-is-custom-translator"></a>Co je Custom Translator?

[Vlastní Překladatel](https://portal.customtranslator.azure.ai) je funkce služby Translator, která umožňuje podnikům, vývojářům aplikací a poskytovatelům jazykových služeb vytvářet přizpůsobené systémy NMT (neuronové Machine Translation). Přizpůsobené systémy překladu se hladce integrují do stávajících aplikací, pracovních postupů a webů. 

Překladatelské systémy vytvořené pomocí [vlastního překladatele](https://portal.customtranslator.azure.ai) jsou k dispozici prostřednictvím stejného cloudového [, vysoce výkonného a vysoce](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)škálovatelného [Azure Cognitive Servicesho překladatele V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), který každý den provede miliardy překlady.

Vlastní Překladatel podporuje více než tři desítkové jazyky a mapuje se přímo na jazyky, které jsou k dispozici pro NMT. Úplný seznam najdete v tématu [jazyky překladatelů](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Funkce

Vlastní Překladatel poskytuje různé funkce pro sestavení vlastního systému překladu a následně k němu přistupuje.

|Funkce  |Popis  |
|---------|---------|
|[Využití technologie strojového překladu neuronové](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Vylepšete svůj překlad využitím neuronové strojového překladu (NMT) poskytovaného vlastním překladatelem.       |
|[Systémy sestavování, které znají vaši podnikovou terminologii](what-are-parallel-documents.md)     |  Přizpůsobte a vytvářejte překladatelské systémy pomocí paralelních dokumentů, které pochopí terminologií používané ve vašem podniku a v průmyslu.       |
|[Použití slovníku k sestavení modelů](what-is-dictionary.md)     |   Pokud nemáte datovou sadu školicích dat, můžete vytvořit model s pouze daty ze slovníku.       |
|[Spolupráce s ostatními](how-to-manage-settings.md#share-your-workspace)     |   Spolupracujte se svým týmem sdílením práce s různými lidmi.     |
|[Přístup k vlastnímu modelu překladu](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  K vašemu vlastnímu modelu překladu můžete kdykoli přistupovat prostřednictvím aplikace Translator v3.       |

## <a name="get-better-translations"></a>Získat lepší překlady

Translator vydal [neuronové strojový překlad (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) v 2016. NMT poskytuje zásadní pokrok v kvalitě překladu v rámci technologie [SMT (Standard statistického strojového překladu)](https://en.wikipedia.org/wiki/Statistical_machine_translation) . Vzhledem k tomu, že NMT lépe zachycuje kontext úplných vět před jejich překladem, poskytuje vyšší kvalitu, lepší lidské zvuky a lepší překlady. [Vlastní Překladatel](https://portal.customtranslator.azure.ai) poskytuje NMT pro vaše vlastní modely, což má za následek lepší kvalitu překladu.

Můžete použít dříve přeložené dokumenty k sestavení systému překladu. Tyto dokumenty zahrnují terminologii a styl specifický pro doménu, což je lepší než standardní systém překladu. Uživatelé můžou nahrávat dokumenty ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX a XLSX.

Vlastní Překladatel taky přijímá data, která jsou na úrovni dokumentu rovnoběžná, aby bylo shromažďování a Příprava dat efektivnější. Pokud uživatelé mají přístup k verzím stejného obsahu v různých jazycích, ale v samostatných dokumentech, vlastní Překladatel bude moci automaticky rozlišovat věty v rámci dokumentů.

Pokud je k dispozici odpovídající typ a množství školicích dat, není běžné zobrazení [skóre Bleu](what-is-bleu-score.md) v rozsahu 5 až 10 bodů pomocí vlastního překladatele.

## <a name="be-productive-and-cost-effective"></a>Být produktivní a nákladově efektivní

V případě [vlastního překladatele](https://portal.customtranslator.azure.ai)nevyžadují školení a nasazení vlastního systému žádné znalosti o programování.

Pomocí portálu zabezpečeného [vlastního překladatele](https://portal.customtranslator.azure.ai) můžou uživatelé nahrávat školicí data, naučit systémy, testovat systémy a nasazovat je do produkčního prostředí prostřednictvím intuitivního uživatelského rozhraní. Systém bude potom k dispozici pro použití ve velkém měřítku během několika hodin (skutečný čas závisí na velikosti školicích dat).

K [vlastnímu překladateli](https://portal.customtranslator.azure.ai) se taky dá programově přistupovat prostřednictvím [vyhrazeného rozhraní API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (aktuálně ve verzi Preview). Rozhraní API umožňuje uživatelům pravidelně spravovat vytváření a aktualizaci průběžných školení prostřednictvím vlastní aplikace nebo webové služby.

Náklady na používání vlastního modelu k překladu obsahu jsou založené na cenové úrovni překladatele uživatele. Podrobnosti o cenové úrovni najdete na [webové stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) pro Cognitive Services Translator.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Bezpečně Přeložte kdykoli a kdekoli na všech svých aplikacích a službách.

Vlastní systémy je možné hladce využít a integrovat do libovolného podnikového a obchodního pracovního postupu a na jakémkoli zařízení prostřednictvím překladatele prostřednictvím nástroje pro standardní technologii REST.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- S [rychlým startem](quickstart-build-deploy-custom-model.md) se naučíte vytvářet model překladu ve vlastním překladateli.
