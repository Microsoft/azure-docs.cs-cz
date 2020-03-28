---
title: Co je Custom Translator?
titleSuffix: Azure Cognitive Services
description: Vlastní překladač nabízí podobné funkce jako to, co microsoft translator hub dělá pro statistický strojový překlad (SMT), ale výhradně pro systémy neurostrojového překladu (NMT).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d84a0190e13d7bb7664e8792e0047338fe4bf5e2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982695"
---
# <a name="what-is-custom-translator"></a>Co je Custom Translator?

[Custom Translator](https://portal.customtranslator.azure.ai) je funkce služby Microsoft Translator, která umožňuje podnikům Translator, vývojářům aplikací a poskytovatelům jazykových služeb vytvářet přizpůsobené systémy neuroelektronického strojového překladu (NMT). Přizpůsobené překladatelské systémy se bezproblémově integrují do stávajících aplikací, pracovních postupů a webových stránek. [Vlastní překladač](https://portal.customtranslator.azure.ai/) nabízí podobné funkce jako [to,](https://hub.microsofttranslator.com/) co microsoft translator hub dělá pro statistický strojový překlad (SMT), ale výhradně pro systémy neurostrojového překladu (NMT).

Překladové systémy vytvořené pomocí [vlastního překladače](https://portal.customtranslator.azure.ai) jsou k dispozici prostřednictvím stejného cloudového, [zabezpečeného,](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)vysoce výkonného a vysoce škálovatelného rozhraní Microsoft Translator [Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), které každý den pohání miliardy překladů.

Vlastní překladač podporuje více než tři desítky jazyků a mapuje přímo na jazyky, které jsou k dispozici pro NMT. Úplný seznam naleznete v tématu [Microsoft Translator Languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Funkce

Custom Translator poskytuje různé funkce pro vytvoření vlastního překladového systému a následný přístup k němu.

|Funkce  |Popis  |
|---------|---------|
|[Využijte technologii nervového strojového překladu](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Zlepšete svůj překlad využitím neurálního strojového překladu (NMT) poskytovaného vlastním překladačem.       |
|[Vytvářejte systémy, které znají vaši obchodní terminologii](what-are-parallel-documents.md)     |  Přizpůsobte a sestavte překladatelské systémy pomocí paralelních dokumentů, které rozumí terminologii používané ve vašem vlastním podnikání a průmyslu.       |
|[Vytvoření modelů pomocí slovníku](what-is-dictionary.md)     |   Pokud nemáte trénovací datovou sadu, můžete trénovat model pouze s daty slovníku.       |
|[Spolupráce s ostatními](how-to-manage-settings.md#share-your-workspace)     |   Spolupracujte se svým týmem tím, že se podělíte o svou práci s různými lidmi.     |
|[Přístup k vlastnímu modelu překladu](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Váš vlastní překlad model lze přistupovat kdykoliv vaše stávající aplikace / programy prostřednictvím Microsoft Translator Text API V3.       |

## <a name="get-better-translations"></a>Získejte lepší překlady

Microsoft Translator vydal v roce 2016 [neural strojový překlad (NMT).](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) NMT poskytla významný pokrok v kvalitě překladů oproti standardní technologii [statistického strojového překladu (SMT).](https://en.wikipedia.org/wiki/Statistical_machine_translation) Vzhledem k tomu, že NMT lépe zachycuje kontext celých vět před jejich překladem, poskytuje vyšší kvalitu, více lidsky znějící a plynulejší překlady. [Vlastní překladač](https://portal.customtranslator.azure.ai) poskytuje NMT pro vaše vlastní modely, což vede k lepší kvalitě překladu.

K vytvoření překladového systému můžete použít dříve přeložené dokumenty. Tyto dokumenty obsahují terminologii a styl specifický pro doménu, lepší než standardní překladový systém. Uživatelé mohou nahrávat dokumenty ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX a XLSX.

Vlastní překladač také přijímá data, která je paralelní na úrovni dokumentu, aby shromažďování dat a příprava efektivnější. Pokud mají uživatelé přístup k verzím stejného obsahu ve více jazycích, ale v samostatných dokumentech, bude vlastní překladač schopen automaticky spárovat věty mezi dokumenty.

Pokud je zadán příslušný typ a množství trénovacích dat, není neobvyklé vidět [skóre BLEU](what-is-bleu-score.md) zisky mezi 5 a 10 body pomocí vlastního překladače.

## <a name="be-productive-and-cost-effective"></a>Buďte produktivní a nákladově efektivní

S [vlastní překladač](https://portal.customtranslator.azure.ai), školení a nasazení vlastního systému nevyžaduje žádné znalosti programování.

Pomocí zabezpečeného portálu [Vlastní překladač](https://portal.customtranslator.azure.ai) mohou uživatelé nahrávat trénovací data, vlakové systémy, testovací systémy a nasazovat je do produkčního prostředí prostřednictvím intuitivního uživatelského rozhraní. Systém pak bude k dispozici pro použití ve velkém měřítku během několika hodin (skutečný čas závisí na velikosti trénovacích dat).

[Vlastní překladač](https://portal.customtranslator.azure.ai) lze také programově přistupovat prostřednictvím [vyhrazené rozhraní API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (aktuálně ve verzi Preview). Rozhraní API umožňuje uživatelům spravovat vytváření nebo aktualizaci školení v pravidelných intervalech prostřednictvím své vlastní aplikace nebo webové služby.

Náklady na použití vlastního modelu k překladu obsahu jsou založeny na cenové úrovni rozhraní TRANSLATOR API uživatele. Podrobnosti o cenové úrovni najdete na [stránce s cenami překladače](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) kognitivních služeb.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Bezpečně překládejte kdykoli a kdekoli ve všech svých aplikacích a službách

K vlastním systémům lze bezproblémově přistupovat a integrovat je do libovolného pracovního postupu produktu nebo firmy a na jakémkoli zařízení prostřednictvím rozhraní Microsoft Translator Text API prostřednictvím standardní technologie REST.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- S [Rychlým startem](quickstart-build-deploy-custom-model.md) se naučte vytvořit model překladu v custom translatoru.
