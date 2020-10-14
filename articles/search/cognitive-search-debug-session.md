---
title: Koncepty relací ladění (Preview)
titleSuffix: Azure Cognitive Search
description: Relace ladění, ke kterým se dostanete prostřednictvím Azure Portal, poskytuje rozhraní IDE, jako je prostředí, ve kterém můžete identifikovat a opravovat chyby, ověřovat změny a doručovat změny do dovednosti v kanálu pro rozšíření AI. Relace ladění jsou ve verzi Preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057616"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Ladění relací v Azure Kognitivní hledání

Relace ladění je vizuální editor, který pracuje s existujícím dovednosti v Azure Portal. V rámci ladicí relace můžete identifikovat a vyřešit chyby, ověřovat změny a doručovat změny do produkčního dovednostiu v kanálu pro rozšíření AI.

> [!Important]
> Relace ladění je funkce ve verzi Preview, která se poskytuje bez smlouvy o úrovni služeb, a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="using-debug-sessions"></a>Použití relací ladění

Když spustíte relaci, služba vytvoří kopii dovednosti, indexeru a indexu, ve kterém se k otestování dovednosti použije jediný dokument. Změny provedené v relaci jsou uloženy do relace. Změny provedené v relaci ladění nebudou mít vliv na produkční dovednosti, pokud se do něj neúčtují změny. Při potvrzení změn dojde k přepsání produkčního dovednostiu.

Během ladicí relace můžete upravit dovednosti, zkontrolovat a ověřit každý uzel ve stromu rozšíření v kontextu konkrétního dokumentu. Až se vyřeší problémy kanálu rozšíření, můžou se změny uložit do relace a potvrdit se do dovednosti v produkčním prostředí. 

Pokud kanál pro obohacení neobsahuje žádné chyby, ladicí relaci lze použít k přírůstkové obohacení dokumentu, otestování a ověření každé změny před potvrzením změn.

## <a name="creating-a-debug-session"></a>Vytvoření relace ladění

Chcete-li spustit ladicí relaci, je nutné mít existující kanál rozšíření AI, včetně; zdroj dat, dovednosti, indexer a index. Chcete-li nakonfigurovat relaci ladění, je nutné pojmenovat relaci a zadat účet úložiště pro obecné účely, který bude použit k ukládání dovedností do mezipaměti během spuštění indexeru. Bude také nutné vybrat indexer, který bude spuštěn. Indexer má odkazy uložené ve zdroji dat, dovednosti a indexu. Relace ladění bude ve výchozím nastavení použita na první dokument ve zdroji dat, nebo můžete do tohoto zdroje zadat dokument, který chcete procházet.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Vytvoření relace ladění":::

## <a name="debug-session-features"></a>Ladění funkcí relace

Relace ladění začíná spuštěním dovednosti ve vybraném dokumentu. Relace ladění bude nahrávat další metadata přidružená k jednotlivým operacím v dovednosti. Metadata vytvořená prováděním dovedností kanálu, informují následující sadu funkcí, které jsou pak použity pro pomoc s identifikací a opravou problémů s dovednosti.

## <a name="ai-enrichments"></a>Rozšíření AI

Jelikož dovednosti spouštějí strom obohacení, který představuje dokument, roste. Použití stromu k vizualizaci výstupů dovedností nebo rozšíření poskytuje komplexní přehled o všech provedených rozšířeních. Můžete hledat v celém dokumentu a zkontrolovat každý uzel stromu obohacení. Tato perspektiva usnadňuje objekty tvarů. Tento formát také poskytuje vizuální pomůcky k typu, cestě a obsahu všech uzlů ve stromu.

## <a name="skill-graph"></a>Graf dovedností

Zobrazení **grafu dovedností** poskytuje hierarchickou a vizuální reprezentaci dovednosti. Graf je horní a dolní reprezentace pořadí, ve kterém se dovednosti spouštějí. Dovednosti, které jsou závislé na výstupu dalších dovedností, se zobrazí v grafu níže. Dovednosti na stejné úrovni hierarchie můžou být spouštěny paralelně. 

Výběr dovednosti v grafu zvýrazní dovednosti, které jsou k ní připojené, uzly, které vytvářejí jeho vstupy, a uzly, které přijímají jeho výstupy. Každý uzel dovedností zobrazuje jeho typ, chyby, varování a počty spuštění. **Graf dovedností** je místo, kde vyberete, která dovednost se má ladit nebo vylepšit. Když vyberete dovednost, zobrazí se v podokně Podrobnosti dovednosti napravo od grafu.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Vytvoření relace ladění":::

## <a name="skill-details"></a>Podrobnosti dovednosti

V podokně Podrobnosti o dovednostech se zobrazuje sada oblastí pro práci s konkrétní dovedností, pokud je tato dovednost v **grafu dovedností**zvýrazněna. Můžete zkontrolovat a upravit podrobnosti o nastavení dovednosti. Definice JSON dovednosti je k dispozici. Zobrazí se také podrobnosti o spuštění dovednosti a chyby a upozornění. Karta **Nastavení dovedností** & **Editor JSON pro dovednosti** umožňuje přímé úpravy dovedností. [`</>`](#expression-evaluator)Otevře se okno pro zobrazení a úpravy výrazů pro vstupy a výstupy dovedností.

Vnořené ovládací prvky vstupu v okně nastavení dovedností lze použít k vytvoření složitých tvarů pro projekce, mapování výstupních polí pro pole komplexního typu nebo zadání dovednosti. Při použití s **vyhodnocovacím filtrem výrazů**poskytují vnořené vstupy jednoduchý test a ověřují Tvůrce výrazů.

## <a name="skill-execution-history"></a>Historie spouštění dovedností

Dovednost se může spouštět víckrát v dovednosti pro jeden dokument. Například dovednost optického rozpoznávání znaků se spustí jednou pro každý obrázek extrahovaný z každého dokumentu. V podokně Podrobnosti o dovednostech se na kartě **spuštění** zobrazuje historie spouštění dovedností, která poskytuje hlubší pohled na každé vyvolání dovednosti. 

Historie spuštění umožňuje sledovat konkrétní obohacení zpět na dovednosti, která ji vygenerovala. Kliknutím na vstup dovedností přejdete k dovednosti, která tento vstup vygenerovala. To umožňuje identifikaci hlavní příčiny problému, který může být v rámci dovedností v rámci podřízenosti. 

Pokud je zjištěn potenciální problém, v historii spuštění se zobrazí odkazy na dovednosti, které vygenerovaly konkrétní vstupy, a poskytuje funkci pro trasování zásobníku, jako je například. Kliknutím na dovednost přidruženou ke vstupu přejdete na dovednost a opravíte případné chyby nebo budete pokračovat v trasování konkrétního problému.

Při sestavování vlastní dovednosti nebo ladění chyby s vlastní dovedností je k dispozici možnost generovat požadavek na vyvolání dovedností v historii spuštění.

## <a name="enriched-data-structure"></a>Obohacená datová struktura

Podokno **obohacení struktury dat** zobrazuje obohacení dokumentu prostřednictvím dovednosti, podrobně popisuje kontext pro každé rozšíření a původní dovednost. **Vyhodnocovací filtr výrazů** lze také použít k zobrazení obsahu pro každé obohacení.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Vytvoření relace ladění":::

## <a name="expression-evaluator"></a>Vyhodnocení výrazu

**Vyhodnocení výrazu** poskytuje rychlý náhled na hodnotu libovolné cesty. Umožňuje upravit cestu a otestovat výsledky před aktualizací jakéhokoli vstupu nebo kontextu pro dovednost nebo projekci.

## <a name="errorswarnings"></a>Chyby a upozornění

V tomto okně se zobrazí všechny chyby a upozornění, které dovednosti generuje, protože se spustí s dokumentem v relaci ladění.

## <a name="limitations"></a>Omezení

Ladění sesisons práce s využitím všech všeobecně dostupných zdrojů dat ve verzi Preview zdrojů dat pro procesory. Rozhraní MongoDB API (Preview) a rozhraní API Cassandra (Preview) Cosmos DB aktuálně nejsou podporovány.

## <a name="next-steps"></a>Další kroky

Teď, když rozumíte prvkům ladicích relací, vyzkoušejte si kurz pro praktické prostředí.

> [!div class="nextstepaction"]
> [Kurz k procházení funkcí cvičení ladění](./cognitive-search-tutorial-debug-sessions.md)