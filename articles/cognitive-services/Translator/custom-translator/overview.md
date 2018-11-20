---
title: Co je vlastní Translator?
titleSuffix: Azure Cognitive Services
description: Vlastní Translator nabízí podobné funkce k Microsoft Translatoru Hub význam pro statistické strojový překlad (SMT), ale výhradně pro Neurální strojový překlad sítí (NMT) systémy.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: overview
ms.openlocfilehash: 76e6bc006ff6049b631409a3515628fbd169f713
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976974"
---
# <a name="what-is-custom-translator-preview"></a>Co je Custom Translator (Preview)?

[Vlastní Translator](https://portal.customtranslator.azure.ai) je funkce služby Microsoft Translator, který umožňuje podnikům Translator, vývojáři aplikací a poskytovatelé služeb jazyka vytvářet přizpůsobené systémy pro strojový překlad neuronových (sítí NMT). Přizpůsobených překladových systémů bez problémů integrovat do stávajících aplikací, pracovní postupy a websites. [Vlastní Translator](https://portal.customtranslator.azure.ai/) nabízí podobné funkce pro co [Microsoft Translator Hub](https://hub.microsofttranslator.com/) nemá pro statistické strojový překlad (SMT), ale výhradně pro Neurální strojový překlad sítí (NMT) systémy.

Sestavován systémů překladu [vlastní Translator](https://portal.customtranslator.azure.ai) jsou k dispozici prostřednictvím stejné založené na cloudu [zabezpečené](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), vysoký výkon, vysoce škálovatelné Microsoft Translatoru [Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), která využívá miliardy překlady každý den. 

Vlastní Translator podporuje více než tři deseti jazycích a mapuje přímo na jazyky dostupné pro NMT. Úplný seznam najdete v tématu [jazyky Microsoft Translatoru]( https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

## <a name="features"></a>Funkce

Vlastní Translator nabízí různé funkce, které vlastní překladový systém sestavení a následně k němu přístup.

|Funkce  |Popis  |
|---------|---------|
|[Využijte technologii Neurální strojový překlad](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Zlepšete váš překlad s využitím Neurální strojový překlad (sítí NMT) poskytuje vlastní překladač.       |
|[Systémy, které zná obchodní terminologii sestavení](what-are-parallel-documents.md)     |  Přizpůsobení a vytváření překladových systémů pomocí paralelní dokumenty, které pochopit terminologie pro firmy a oboru.       |
|[Použití slovníku k vytváření modelů.](what-is-dictionary.md)     |   Pokud nemáte trénovací datové sady, které vyškolíme model pomocí pouze data slovníku.       |
|[Spolupráce s ostatními](how-to-manage-settings.md#share-your-workspace)     |   Spolupracujte se svým týmem při sdílení práce s různým lidem.     |
|[Přístup k vaší vlastní překladu modelu](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Váš vlastní překlad model je přístupný kdykoli svoje stávající aplikace nebo programy prostřednictvím Microsoft Translator Text API V3.       |

## <a name="get-better-translations"></a>Získejte lepší překlady

Microsoft Translator vydané [Neurální strojový překlad sítí (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) 2016. NMT získanou standard odvětví v oblasti hlavní záloh v kvalitu překladu [statistické strojový překlad (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) technologie. Vzhledem k tomu NMT lépe zachytí kontextu úplné věty před jejich překladu, poskytuje vyšší kvality, lidské zní a více fluent překlady. [Vlastní Translator](https://portal.customtranslator.azure.ai) poskytuje NMT pro vaše vlastní modely výsledný lepší kvalitu překladu.

Dříve přeložených dokumentů můžete použít k sestavení překladový systém. Tyto dokumenty zahrnují lepší výsledky než obecné překladový systém specifického pro doménu terminologie a stylu. Uživatelé můžou nahrávat dokumenty, zarovnání, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX a XLSX.

Vlastní Translator přijímá také data, která je paralelní na úrovni dokumentu ke shromažďování dat a jejich přípravu efektivnější. Pokud uživatelé mají přístup k verzím stejný obsah ve více jazycích, ale v samostatných dokumentech, vlastní Translator budou moci automaticky odpovídat věty v dokumentech.

Pokud je zadán odpovídající typ a velikost trénovacích dat, není nic neobvyklého, naleznete v tématu [BLEU skóre](what-is-bleu-score.md) zisky mezi 5 až 10 bodů s použitím překladač vlastní.

## <a name="be-productive-and-cost-effective"></a>Produktivitu a nákladově efektivní

S [vlastní Translator](https://portal.customtranslator.azure.ai), školení a nasazení vlastních systému nevyžaduje žádné znalosti programování. 

Používat zabezpečená [vlastní Translator](https://portal.customtranslator.azure.ai) portálu, uživatelé mohou nahrát trénovacích dat, trénování systémy, testovací systémy a jejich nasazení do produkčního prostředí prostřednictvím intuitivního uživatelského rozhraní. Systém pak bude k dispozici pro použití ve velkém měřítku během několika hodin (skutečná doba závisí na školení velikost dat).

[Vlastní Translator](https://portal.customtranslator.azure.ai) také programově přistupuje prostřednictvím [vyhrazené API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (aktuálně ve verzi preview). Rozhraní API umožňuje uživatelům spravovat vytváření nebo aktualizace školení v pravidelných intervalech prostřednictvím vlastní aplikace nebo webové služby.

Náklady na použití vlastního modelu pro převod obsahu je založen na cenovou úroveň uživatelské rozhraní Translator Text API. Zobrazit služeb Cognitive Services [ceny webové stránky rozhraní Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) úroveň podrobnosti o cenách.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Bezpečně přeložit kdykoli a kdekoli na všechny vaše aplikace a služby

Vlastní systémy můžete bez problémů přistupovat a integrované do všech pracovních postupů v produktu nebo obchodní a na libovolném zařízení přes Microsoft Translator Text API prostřednictvím standardních technologii REST.

## <a name="next-steps"></a>Další postup

- Přečtěte si [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- S [rychlý Start](quickstart-build-deploy-custom-model.md) se naučíte, jak sestavit model překlad ve vlastní překladač.
