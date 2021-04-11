---
title: Sémantické vyhledávání
titleSuffix: Azure Cognitive Search
description: Přečtěte si, jak Kognitivní hledání používá sémantické vyhledávací modely pro rozsáhlou výuku z Bingu, aby byly výsledky hledání intuitivnějšíější.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2021
ms.custom: references_regions
ms.openlocfilehash: 04300b8d148bb22bf585aa81481c475b347ad462
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222040"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Sémantické vyhledávání v Azure Kognitivní hledání

> [!IMPORTANT]
> Sémantické vyhledávání je ve verzi Public Preview, které je dostupné prostřednictvím REST API a portálu ve verzi Preview. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)a nemají zaručenou stejnou implementaci při obecné dostupnosti. Tyto funkce jsou Fakturovatelné. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Sémantické vyhledávání je kolekce funkcí souvisejících s dotazy, které přidávají výsledkům vyhledávání sémantickou relevanci a jazykové porozumění. *Sémantické hodnocení* vyhledává kontext a souvislost mezi podmínkami a zvyšuje shody, které dávají dotazu větší smysl. Při použití jazyka se vyhledává *popisy* a *odpovědi* v rámci vašeho obsahu, které shrnují odpovídající dokument nebo odpovídají na otázku, která se pak dá vykreslit na stránce s výsledky hledání, aby bylo lépe produktivní.

Špičkové modely s předvýukou se používají pro Shrnutí a hodnocení. Pro zajištění rychlého výkonu, který uživatelé očekávají od hledání, se sémantické sumarizace a hodnocení aplikují jenom na prvních 50 výsledků, které se vyhodnotí jako [výchozí algoritmus bodování podobnosti](index-similarity-and-scoring.md#similarity-ranking-algorithms). Použití těchto výsledků jako corpus dokumentu, sémantické hodnocení vede ke změně skóre výsledků na základě sémantické síly shody.

Základní technologie pochází z Bingu a Microsoft Research a je integrovaná do infrastruktury Kognitivní hledání jako doplňková funkce. Další informace o sémantickém vyhledávání v oblasti výzkumu a AI pro zálohování najdete v tématu [jak AI z Bingu používá Azure kognitivní hledání (blog Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Následující video poskytuje přehled možností.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Komponenty a pracovní postup

Sémantické vyhledávání zlepšuje přesnost a odvolání přidáním následujících možností:

| Funkce | Popis |
|---------|-------------|
| [Kontrola pravopisu](speller-how-to-add.md) | Opraví překlepy dříve, než se dostanou do vyhledávacího modulu. |
| [Sémantické hodnocení](semantic-ranking.md) | Používá kontext nebo sémantický význam k výpočtu nového skóre relevance. |
| [Sémantické titulky a světla](semantic-how-to-query-request.md) | Věty a fráze z dokumentu, které nejlépe shrnují obsah, s důrazem na nejdůležitější pasáže pro snadné prohledávání. Popisky, které shrnují výsledek, jsou užitečné, když jsou jednotlivá pole obsahu pro stránku výsledků moc zhuštěná. Zvýrazněný text zvyšuje nejdůležitější výrazy a fráze, aby uživatelé mohli rychle zjistit, proč se shoda považuje za relevantní. |
| [Sémantické odpovědi](semantic-answers.md) | Volitelná a další podstruktura vrácená sémantickým dotazem. Poskytuje přímou odpověď na dotaz, který vypadá jako otázka. |

### <a name="order-of-operations"></a>Pořadí operací

Komponenty sémantického hledání rozšíří existující kanál spuštění dotazu v obou směrech. Pokud povolíte opravu pravopisu, [Kontrola pravopisu](speller-how-to-add.md) před dosažením vyhledávacího modulu opraví překlepy při vynechání dotazu.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Sémantické komponenty v provádění dotazů" border="true":::

Provádění dotazů bude v obvyklých případech pokračovat s analýzou, analýzou a prověřováním obrácených indexů. Modul načítá dokumenty pomocí odpovídajícího tokenu a skóre výsledků používá [výchozí algoritmus bodování podobnosti](index-similarity-and-scoring.md#similarity-ranking-algorithms). Skóre se počítají na základě stupně jazykové podobnosti mezi výrazy dotazu a odpovídajícími podmínkami v indexu. Pokud jste je definovali, budou se v této fázi také použít profily bodování. Výsledky se pak předávají do subsystému sémantického vyhledávání.

V kroku přípravy se dokument corpus vrácený z počáteční sady výsledků analyzuje na úrovni věty a odstavce, kde najdete pasáže shrnující jednotlivé dokumenty. Na rozdíl od hledání klíčových slov tento krok vyhodnocuje obsah pomocí čtení a porozumění počítače. V této fázi zpracování obsahu vrátí sémantický dotaz [titulky](semantic-how-to-query-request.md) a [odpovědi](semantic-answers.md). Sémantické vyhledávání používá k extrakci a zvýraznění klíčových pasáží, které nejlépe shrnují výsledek, použití jazykové reprezentace. Pokud je vyhledávací dotaz požadován a jsou požadovány odpovědi, bude odpověď zahrnovat také text pasáž, který nejlépe odpovídá otázce, jak je vyjádřen vyhledávacím dotazem. 

U titulků a odpovědí se v formulaci používá existující text. Sémantické modely nevytvářejí nové věty nebo fráze z dostupného obsahu ani nepoužívají logiku pro doručení nových závěrů. V krátkém případě systém nikdy nevrátí obsah, který ještě neexistuje.

Výsledky se pak převrátí na základě [rámcové podobnosti](semantic-ranking.md) podmínek dotazu.

Pokud chcete v dotazech používat sémantické možnosti, budete muset provést drobné úpravy [žádosti o vyhledávání](semantic-how-to-query-request.md), ale není nutná žádná další konfigurace ani změna indexu.

## <a name="availability-and-pricing"></a>Dostupnost a ceny

Sémantické možnosti jsou k dispozici prostřednictvím [registrace registrace](https://aka.ms/SemanticSearchPreviewSignup), a to u vyhledávacích služeb vytvořených na úrovni Standard (S1, S2, S3), které se nacházejí v jedné z těchto oblastí: Střed USA – sever, Západní USA, Západní USA 2, Východní USA 2, Severní Evropa, západní Evropa. 

Oprava pravopisu je k dispozici ve stejných oblastech, ale nemá žádná omezení vrstvy. Máte-li existující službu, která splňuje kritéria pro úroveň a oblast, je vyžadována pouze registrace.

V období od 2. března od do 12. dubna se nabízí Oprava pravopisu a sémantické hodnocení bez poplatků. Později v dubnu se výpočetní náklady na spuštění této funkce stanou fakturovatelnými událostmi. Očekávané náklady jsou přibližně USD $500 měsíčně za dotazy 250 000. Podrobné informace o nákladech, které jsou popsány na [stránce s cenami kognitivní hledání](https://azure.microsoft.com/pricing/details/search/) , najdete v části [odhad a Správa nákladů](search-sku-manage-costs.md).

## <a name="next-steps"></a>Další kroky

Nový typ dotazu povoluje hodnocení relevance a struktury odpovědí sémantického hledání.

Pokud chcete začít, [vytvořte sémantický dotaz](semantic-how-to-query-request.md) . Případně si přečtěte následující články, kde najdete související informace.

+ [Přidat kontrolu pravopisu pro dotaz na výrazy](speller-how-to-add.md)
+ [Vrátí sémantickou odpověď.](semantic-answers.md)
+ [Sémantické hodnocení](semantic-ranking.md)
+ [Představujeme sémantické vyhledávání (příspěvek na blogu)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Vyhledávejte smysluplné přehledy pomocí sémantických schopností (video zobrazit AI).](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)