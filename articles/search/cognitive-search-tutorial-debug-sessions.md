---
title: 'Kurz: použití relací ladění k opravě dovednosti'
titleSuffix: Azure Cognitive Search
description: Relace ladění (Preview) je Azure Portal nástroj, který slouží k hledání, diagnostice a opravě problémů v dovednosti.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509138"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Kurz: ladění dovednosti pomocí relací ladění

Dovednosti koordinuje řadu akcí, které analyzují nebo transformují obsah, kde výstup jedné dovednosti se bude vstupem jiného. Když vstupy závisejí na výstupech, chyby v definicích dovednosti a přidružení polí mohou mít za následek zmeškané operace a data.

**Ladicí relace** v Azure Portal poskytují holistický vizualizaci dovednosti. Pomocí tohoto nástroje můžete přejít k určitým krokům a snadno zjistit, kde se může akce rozvést.

V tomto článku použijete **relace ladění** k vyhledání a opravě 1) mapování polí pro výstup chybějícího vstupu a 2). Tento kurz je vše včetně. Poskytuje data pro index (data klinických pokusů), kolekci post, která vytváří objekty a pokyny pro použití **relací ladění** k vyhledání a opravě problémů v dovednosti.

> [!Important]
> Relace ladění je funkce ve verzi Preview, která se poskytuje bez smlouvy o úrovni služeb, a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Požadavky

Než začnete, vydejte tyto požadavky:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

+ Účet Azure Storage s [úložištěm objektů BLOB](../storage/blobs/index.yml), který se používá pro hostování ukázkových dat, a pro zachování dočasných dat vytvořených během relace ladění.

+ Položte [desktopovou aplikaci](https://www.getpostman.com/) a [kolekci post](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) pro vytváření objektů pomocí rozhraní REST API.

+ [Ukázková data (klinická hodnocení)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> V tomto rychlém startu se také pro AI používá [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) . Vzhledem k tomu, že úloha je tak malá, Cognitive Services po dobu bezplatného zpracování po dobu až 20 transakcí klepnuli na pozadí. To znamená, že můžete dokončit toto cvičení, aniž byste museli vytvořit další prostředek Cognitive Services.

## <a name="set-up-your-data"></a>Nastavení dat

Tato část vytvoří ukázkovou datovou sadu v úložišti objektů BLOB v Azure tak, aby indexer a dovednosti měly obsah, se kterým se bude pracovat.

1. [Stáhněte si ukázková data (klinická hodnocení-PDF-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), která se skládají z 19 souborů.

1. [Vytvořte si účet Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) nebo [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Vyberte stejnou oblast jako Azure Kognitivní hledání, abyste se vyhnuli poplatkům za šířku pásma.

   + Vyberte typ účtu StorageV2 (pro obecné účely v2).

1. Na portálu přejděte na stránky služby Azure Storage Services a vytvořte kontejner objektů BLOB. Osvědčeným postupem je určit úroveň přístupu private. Pojmenujte svůj kontejner `clinicaltrialdataset` .

1. V části kontejner klikněte na **Odeslat** a nahrajte ukázkové soubory, které jste stáhli, a v prvním kroku odhlaste.

1. Na portálu Získejte a uložte připojovací řetězec pro Azure Storage. Budete ho potřebovat pro REST API volání, která indexují data. Připojovací řetězec můžete získat z **Nastavení**  >  **přístupových klíčů** na portálu.

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Získání koncového bodu HTTP a přístupového klíče" border="false":::

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="create-data-source-skillset-index-and-indexer"></a>Vytváření zdrojů dat, dovednosti, indexů a indexerů

V této části se účtují a poskytnutá kolekce používají k vytvoření zdroje dat Kognitivní hledání, dovednosti, indexu a indexeru. Pokud nejste obeznámeni s nástrojem post, přečtěte si [Tento rychlý Start](search-get-started-rest.md).

K dokončení této úlohy budete potřebovat vytvořenou [kolekci po](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) vytvoření tohoto kurzu. 

1. Spusťte post a importujte kolekci. V části **soubory**  >  **nové** vyberte kolekci, kterou chcete importovat.
1. Po importu kolekce rozbalte seznam akce (...).
1. Klikněte na **Upravit**.
1. V části aktuální hodnota zadejte název `searchService` (například pokud je koncový bod `https://mydemo.search.windows.net` , název služby je " `mydemo` ").
1. Zadejte `apiKey` buď primární, nebo sekundární klíč vyhledávací služby.
1. Zadejte na `storageConnectionString` stránce klíče účtu Azure Storage.
1. Zadejte `containerName` pro kontejner, který jste vytvořili v účtu úložiště, a pak klikněte na **aktualizovat**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Upravit proměnné v post":::

Kolekce obsahuje čtyři různá volání REST, která se používají k vytváření objektů používaných v tomto kurzu.

První volání vytvoří zdroj dat. `clinical-trials-ds`. Druhé volání vytvoří dovednosti, `clinical-trials-ss` . Třetí volání vytvoří index, `clinical-trials` . Čtvrtý a poslední hovor vytvoří indexer, `clinical-trials-idxr` .

+ Jednotlivé požadavky otevřete tak, že kliknete na **Odeslat** a odešlete všechny žádosti do vyhledávací služby. 

Po dokončení všech volání v kolekci zavřete po a vraťte se do Azure Portal.

## <a name="check-results-in-the-portal"></a>Výsledky kontroly na portálu

Vzorový kód záměrně vytvoří index ladění jako důsledek problémů, ke kterým došlo během provádění dovednosti. Problému chybí data. 

1. V Azure Portal na stránce Přehled služby Search vyberte kartu **indexy** . 
1. Vyberte `clinical-trials` index.
1. Zadejte tento řetězec dotazu: `$select=metadata_storage_path, organizations, locations&$count=true` pro vrácení polí pro konkrétní dokumenty (identifikované jedinečným `metadata_storage_path` polem).
1. Kliknutím na tlačítko **Hledat** spustíte dotaz, vrátíte všechny 19 dokumentů a zobrazíte prázdné hodnoty "organizace" a "umístění".

Tato pole by se měla naplnit pomocí dovednosti pro [rozpoznávání entit](cognitive-search-skill-entity-recognition.md)dovednosti, která se používá k hledání organizací a umístění kdekoli v rámci obsahu objektu BLOB. V dalším cvičení určíte, co se stalo, pomocí relace ladění.

Dalším způsobem, jak prozkoumat chyby a upozornění, je Azure Portal.

1. Otevřete kartu **indexery** a vyberte `clinical-trials-idxr` .
1. Všimněte si, že během celkové úspěšnosti úlohy indexeru se vyskytla 57 upozornění.
1. Kliknutím na tlačítko **úspěch** zobrazíte upozornění (pokud došlo k většinou chyb, odkaz na podrobnosti by **se nezdařil**). Zobrazí se dlouhý seznam všech upozornění vygenerovaných indexerem.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="Zobrazit upozornění":::

## <a name="start-your-debug-session"></a>Spuštění relace ladění

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="spustit novou relaci ladění":::

1. Na stránce Přehled hledání klikněte na kartu **relace ladění** .
1. Vyberte **+ Nová relace ladění**.
1. Pojmenujte relaci. 
1. Připojte relaci k vašemu účtu úložiště. 
1. V šabloně indexer zadejte název indexeru. Indexer má odkazy na zdroj dat, dovednosti a index.
1. Přijměte výchozí volbu dokumentu pro první dokument v kolekci. 
1. **Uložte** relaci. Uložení relace aktivuje kanál obohacení AI podle definice dovednosti.

> [!Important]
> Ladicí relace funguje pouze s jedním dokumentem. Můžete zvolit, který dokument se má ladit, nebo jenom první použít.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Po dokončení inicializace relace ladění se ve výchozím nastavení relace karta **rozšíření AI** a zvýrazní **graf dovedností**. Graf dovedností poskytuje vizuální hierarchii dovednosti a pořadí spouštění postupně a paralelně.

## <a name="find-issues-with-the-skillset"></a>Hledání problémů s dovednosti

Všechny problémy nahlášené indexerem najdete na kartě sousední **chyby a upozornění** . 

Všimněte si, že karta **chyby a upozornění** poskytne mnohem menší seznam než ten, který byl zobrazen dříve, protože tento seznam obsahuje pouze podrobné informace o chybách pro jeden dokument. Podobně jako seznam zobrazený indexerem můžete kliknout na zprávu upozornění a zobrazit podrobnosti tohoto upozornění.

Pokud chcete zkontrolovat oznámení, vyberte **chyby nebo upozornění** . Měli byste vidět tři:

   + Nepovedlo se namapovat výstupní pole umístění do indexu vyhledávání. Podívejte se na vlastnost ' outputFieldMappings ' svého indexeru.
Chybí hodnota '/Document/merged_content/Locations '.

   + "Nepovedlo se namapovat výstupní pole organizace do indexu vyhledávání. Podívejte se na vlastnost ' outputFieldMappings ' svého indexeru.
Chybí hodnota '/Document/merged_content/Organizations '.

   + "Provedené dovednosti byly provedeny, ale mohou mít neočekávané výsledky, protože jeden nebo více vstupů dovedností bylo neplatných.
Chybí Volitelný vstup dovednosti. Název: ' languageCode ', zdroj: '/document/languageCode '. Problémy s analýzou jazyka výrazů: chybí hodnota '/document/languageCode '.

   Mnoho dovedností má parametr languageCode. Kontrolou operace vidíte, že tento vstupní kód jazyka chybí v `Enrichment.NerSkillV2.#1` , což je stejná dovednost pro rozpoznávání entit, která má potíže s výstupem Locations a Organizations. 

Vzhledem k tomu, že všechna tři oznámení se týkají této dovednosti, je dalším krokem ladění této dovednosti. Pokud je to možné, začněte tím, že nejprve vyřešíte problémy se vstupem před přechodem na outputFieldMapping problémy.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Začala nová ladicí relace.":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Opravit chybějící vstupní hodnotu dovednosti

Na kartě **chyby/upozornění** došlo k chybě pro operaci s označením `Enrichment.NerSkillV2.#1` . Podrobnosti o této chybě popisují, že došlo k potížím se vstupní hodnotou dovednosti '/document/languageCode '. 

1. Vraťte se na kartu **obohacení AI** .
1. Klikněte na **graf dovedností**.
1. Kliknutím na dovednost označený **#1** zobrazíte její podrobnosti v pravém podokně.
1. Vyhledejte vstup "languageCode".
1. Vyberte **</>** symbol na začátku řádku a otevřete vyhodnocovací filtr výrazů.
1. Kliknutím na tlačítko **vyhodnocení** potvrďte, že tento výraz má za následek chybu. Ověří, že vlastnost "languageCode" není platný vstup.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Vyhodnocovač výrazů":::

Existují dva způsoby, jak tuto chybu v relaci prozkoumat. První je podívat se na to, odkud vstup přichází – jakou odbornost v hierarchii by měl tento výsledek vyvolat? Karta spuštění v podokně Podrobnosti dovednosti by měla zobrazit zdroj vstupu. Pokud neexistuje žádný zdroj, znamená to, že došlo k chybě mapování pole.

1. Klikněte na kartu **spuštění** .
1. Podívejte se na vstupy a vyhledejte "languageCode". Pro tento vstup není uveden žádný zdroj. 
1. Přepněte v levém podokně, aby se zobrazila obohacená datová struktura. Neexistuje žádná mapovaná cesta odpovídající "languageCode".

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Obohacená datová struktura":::

Pro jazyk je namapovaná cesta. V nastavení dovedností proto dojde k překlepu. Chcete-li tento výraz opravit v #1 dovednosti s výrazem '/Document/Language ', bude nutné aktualizovat.

1. Otevřete vyhodnocovací filtr výrazů **</>** pro jazykovou cestu.
1. Zkopírujte výraz. Zavřete okno.
1. Přejít na nastavení dovedností pro #1 dovednost a otevřete vyhodnocovací filtr výrazů **</>** pro vstup "languageCode".
1. Do pole výraz vložte novou hodnotu/Document/Language a klikněte na **vyhodnotit**.
1. Měl by se zobrazit správný vstup "en". Pokud chcete výraz aktualizovat, klikněte na použít.
1. Klikněte na **Uložit** v podokně Podrobnosti o dovednostech vpravo.
1. V nabídce okna relace klikněte na **Spustit** . Tím se zahájí další spuštění dovednosti s použitím dokumentu. 

Po dokončení spuštění relace ladění klikněte na kartu chyby/upozornění a zobrazí se zpráva s označením "obohacení. NerSkillV2. #1". Nicméně stále existují dvě upozornění, která by služba nemohla mapovat výstupní pole pro organizace a umístění do indexu vyhledávání. Chybí hodnoty: '/Document/merged_content/Organizations ' a '/Document/merged_content/Locations '.

## <a name="fix-missing-skill-output-values"></a>Oprava chybějících výstupních hodnot dovedností

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Chyby a upozornění":::

Z dovednosti chybí výstupní hodnoty. Chcete-li identifikovat dovednost s chybou přejít do rozšířené datové struktury, vyhledejte název hodnoty a podívejte se na jeho původní zdroj. V případě chybějících organizací a hodnot umístění se jedná o výstupy #1ch dovedností. Otevření vyhodnocovacího filtru výrazů </> pro každou cestu zobrazí výrazy uvedené jako "/Document/Content/Organizations" a "/Document/Content/Locations" (v uvedeném pořadí).

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Entita vyhodnocení výrazu – entita organizace":::

Výstup těchto entit je prázdný a neměl by být prázdný. Jaké jsou vstupy vytvářející tento výsledek?

1. Přejít do **grafu dovednosti** a vyberte dovednost #1.
1. V pravém podokně podrobností dovednosti vyberte kartu **spuštění** .
1. Otevřete vyhodnocovací filtr výrazů **</>** pro vstup "text".

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Vstup pro textovou dovednost":::

Zobrazený výsledek tohoto vstupu nevypadá jako textový vstup. Vypadá jako obrázek, který je ohraničen novými řádky. Nedostatek textu znamená, že není možné identifikovat žádné entity. V hierarchii dovednosti se zobrazuje obsah nejprve zpracován dovedností #6 (OCR) a pak se předává do dovednosti #5 (sloučení). 

1. Vyberte dovednost #5 (sloučit) v **grafu dovedností**.
1. Vyberte kartu **spuštění** v pravém podokně podrobností o dovednostech a otevřete vyhodnocovací filtr výrazů **</>** pro výstupy "mergedText".

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Výstup pro dovednost sloučení":::

Zde je text spárován s obrázkem. Podívejte se na výraz "/Document/merged_content", zobrazí se chyba v cestách "organizace" a "umístění" pro #1 dovednost. Místo použití '/Document/Content ' by mělo být pro vstupy "text" použito '/Document/merged_content '.

1. Zkopírujte výraz pro výstup "mergedText" a zavřete okno vyhodnocovacího filtru výrazů.
1. Vyberte dovednost #1 v **grafu dovednosti**.
1. Vyberte kartu **Nastavení dovedností** v pravém podokně podrobností o dovednostech.
1. Otevřete vyhodnocovací filtr výrazů **</>** pro vstup "text".
1. Vložte nový výraz do pole. Klikněte na možnost **vyhodnotit**.
1. Měl by se zobrazit správný vstup s přidaným textem. Chcete-li aktualizovat nastavení dovedností, klikněte na tlačítko **použít** .
1. Klikněte na **Uložit** v podokně Podrobnosti o dovednostech vpravo.
1. V nabídce okna relace klikněte na **Spustit** . Tím se zahájí další spuštění dovednosti s použitím dokumentu.

Po skončení běhu indexeru jsou chyby stále k dispozici. Vraťte se k dovednostem #1 a prozkoumejte je. Vstup pro dovednost byl opraven na ' merged_content ' z ' Content '. Jaké jsou výstupy těchto entit v dovednostech?

1. Vyberte kartu **rozšíření AI** .
1. Vyberte **graf dovedností** a klikněte na #1 dovednosti.
1. Přejděte na **Nastavení dovedností** a vyhledejte "výstupy".
1. Otevřete vyhodnocovací filtr výrazů **</>** pro entitu "organizace".

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Výstup pro entitu organizace":::

Vyhodnocení výsledku výrazu dává správný výsledek. Dovednost pracuje na určení správné hodnoty pro entitu "organizace". Mapování výstupu v cestě entity však stále vyvolává chybu. Při porovnávání výstupní cesty v dovednosti s výstupní cestou v této chybě se jedná o dovednosti, které jsou nadřazené výstupům, organizacím a umístěním pod uzlem/Document/Content. Mapování polí pro výstup očekává, že výsledky budou nadřazené v uzlu/Document/merged_content. V předchozím kroku se vstup změnil z '/Document/Content ' na '/Document/merged_content '. Kontext v nastavení dovedností se musí změnit, aby se zajistilo, že se výstup vygeneruje pomocí správného kontextu.

1. Vyberte kartu **rozšíření AI** .
1. Vyberte **graf dovedností** a klikněte na #1 dovednosti.
1. Přejděte na **Nastavení dovedností** a vyhledejte "Context".
1. Poklikejte na nastavení "Context" a upravte ho pro čtení/Document/merged_content.
1. Klikněte na **Uložit** v podokně Podrobnosti o dovednostech vpravo.
1. V nabídce okna relace klikněte na **Spustit** . Tím se zahájí další spuštění dovednosti s použitím dokumentu.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Oprava kontextu v nastavení dovedností":::

Všechny chyby byly vyřešeny.

## <a name="commit-changes-to-the-skillset"></a>Potvrdit změny do dovednosti

Po zahájení relace ladění vytvořila vyhledávací služba kopii dovednosti. K tomu bylo potřeba chránit původní dovednosti ve vaší vyhledávací službě. Teď, když jste dokončili ladění vašeho dovednostiu, je možné opravy potvrdit (přepsat původní dovednosti). 

Případně, pokud nejste připraveni na potvrzení změn, můžete relaci ladění Uložit a znovu ji otevřít později.

1. V nabídce hlavní relace ladění klikněte na **Potvrdit změny** .
1. Kliknutím na **OK** potvrďte, že chcete aktualizovat dovednosti.
1. Zavřete relaci ladění a vyberte kartu **indexery** .
1. Otevřete okno klinická hodnocení – idxr.
1. Klikněte na **resetovat**.
1. Klikněte na **Spustit**. Potvrďte kliknutím na tlačítko **OK** .

Po skončení běhu indexeru by mělo být zelené zaškrtnutí a slovo úspěšné vedle časového razítka posledního spuštění na kartě **historie spuštění** . Chcete-li zajistit, že byly provedeny změny:

1. Na stránce Přehled hledání vyberte kartu **index** .
1. Otevřete index klinických pokusů a na kartě Průzkumník služby Search zadejte tento řetězec dotazu: `$select=metadata_storage_path, organizations, locations&$count=true` a vraťte pole pro konkrétní dokumenty (identifikované jedinečným `metadata_storage_path` polem).
1. Klikněte na **Vyhledat**.

Výsledky by měly ukazovat na to, že organizace a umístění jsou nyní vyplněny pomocí očekávaných hodnot.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jsme se dodotkli různých aspektů definice a zpracování dovednosti. Další informace o konceptech a pracovních postupech najdete v následujících článcích:

+ [Postup při mapování polí výstupu dovednosti na pole ve vyhledávacím indexu](cognitive-search-output-field-mapping.md)

+ [Dovednosti v Azure Kognitivní hledání](cognitive-search-working-with-skillsets.md)

+ [Jak nakonfigurovat ukládání do mezipaměti pro přírůstkové obohacení](cognitive-search-incremental-indexing-conceptual.md)