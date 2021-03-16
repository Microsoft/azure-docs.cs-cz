---
title: Použití značek tabulky k učení vlastního modelu formuláře – rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Naučte se efektivně používat popisky značek tabulek pod dohledem.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467817"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Použití značek tabulky k učení vlastního modelu formuláře

V tomto článku se naučíte, jak pomocí značek tabulky (popisky) vytvořit vlastní model formuláře. Některé scénáře vyžadují komplexnější popisky, než stačí zarovnávat páry klíč-hodnota. Mezi takové scénáře patří extrakce informací z formulářů se složitými hierarchickými strukturami nebo zjištění položek, které služba automaticky nerozpoznala a neextrahuje. V těchto případech můžete použít značky tabulky k učení vlastního modelu formuláře.

## <a name="when-should-i-use-table-tags"></a>Kdy mám použít značky tabulky?

Tady je několik příkladů použití značek tabulek, které by byly vhodné:

- Data, která chcete extrahovat, jsou prezentována jako tabulky ve formulářích a struktura tabulek je smysluplná. Každý řádek tabulky například představuje jednu položku a každý sloupec řádku představuje konkrétní funkci této položky. V tomto případě můžete použít značku tabulky, kde sloupec představuje funkce a řádek představuje informace o jednotlivých funkcích.
- Data, která chcete extrahovat, nejsou prezentována v konkrétních polích formuláře, ale sémanticky se mohou vejít do dvourozměrné mřížky. Například váš formulář má seznam lidí a obsahuje, jméno, příjmení a e-mailovou adresu. Tyto informace byste chtěli extrahovat. V takovém případě můžete použít značku tabulky s křestním jménem, názvem příjmení a e-mailovou adresou jako sloupci a každý řádek je vyplněný informacemi o osobě ze seznamu.

> [!NOTE]
> Nástroj pro rozpoznávání formulářů automaticky vyhledá a extrahuje všechny tabulky v dokumentech, zda jsou tabulky označeny nebo nikoli. Proto nemusíte označovat každou tabulku z formuláře pomocí značky tabulky a značky tabulky nemusejí replikovat strukturu velmi tabulky nalezené ve formuláři. Tabulky extrahované automaticky nástrojem pro rozpoznávání formulářů budou zahrnuty v části pageResults výstupu JSON.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Vytvoření značky tabulky pomocí nástroje test rozpoznávání OCR ve formě (FOTT)
<!-- markdownlint-disable MD004 -->
* Určete, zda chcete vytvořit značku tabulky **dynamické** nebo **pevné velikosti** . Pokud se počet řádků liší od dokumentu k dokumentu, použijte značku dynamické tabulky. Pokud je počet řádků konzistentní v rámci dokumentů, použijte značku tabulky s pevnou velikostí.
* Pokud je značka tabulky dynamická, definujte názvy sloupců a datový typ a formát pro každý sloupec.
* Pokud má tabulka pevnou velikost, zadejte název sloupce, název řádku, datový typ a formát pro každou značku.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Konfigurace značky tabulky":::

## <a name="label-your-table-tag-data"></a>Popisek dat značek tabulky

* Pokud má váš projekt značku tabulky, můžete otevřít panel pro popis a naplnit značku jako pole klíč-hodnota.
:::image type="content" source="media/table-labeling.png" alt-text="Označení pomocí značek tabulky":::

## <a name="next-steps"></a>Další kroky

Postupujte podle našeho rychlého startu a využijte vlastní model pro rozpoznávání formulářů:

> [!div class="nextstepaction"]
> [Výuka pomocí popisků pomocí ukázkového nástroje pro označování](quickstarts/label-tool.md)

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](overview.md)
>
