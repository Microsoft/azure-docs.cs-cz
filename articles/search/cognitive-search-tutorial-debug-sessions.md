---
title: 'Kurz: použití relací ladění ke diagnostikování, opravě a potvrzení změn ve vašem dovednosti'
titleSuffix: Azure Cognitive Search
description: Relace ladění (Preview) poskytují rozhraní založené na portálu pro vyhodnocení a opravu problémů/chyb v dovednosti.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8ec39c4616f5a34f8326b56d4f0ba6e15cdad91c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699113"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Kurz: Diagnostika, oprava a potvrzení změn ve vašem dovednosti

V tomto článku použijete Azure Portal k přístupu k relacím ladění, abyste opravili problémy s poskytnutým dovednosti. Dovednosti obsahuje nějaké chyby, které je potřeba řešit. Tento kurz vás provede ladicí relací k identifikaci a řešení problémů se vstupy a výstupy v dovednostech.

> [!Important]
> Relace ladění je funkce ve verzi Preview, která se poskytuje bez smlouvy o úrovni služeb, a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Vytvoření [bezplatného účtu](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nebo použití aktuálního předplatného
> * Instance služby Azure Kognitivní hledání
> * Účet Azure Storage
> * [Desktopová aplikace Postman](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento kurz používá služby Azure Kognitivní hledání a Azure Storage.

* [Stáhněte si ukázková data](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) skládající se z 19 souborů.

* [Vytvořte si účet Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) nebo [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Vyberte stejnou oblast jako Azure Kognitivní hledání, abyste se vyhnuli poplatkům za šířku pásma.
   
   Vyberte typ účtu StorageV2 (pro obecné účely v2).

* Otevřete stránky služby Storage a vytvořte kontejner. Osvědčeným postupem je určit úroveň přístupu private. Pojmenujte svůj kontejner `clinicaltrialdataset` .

* V části kontejner klikněte na **Odeslat** a nahrajte ukázkové soubory, které jste stáhli, a v prvním kroku odhlaste.

* [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro tento rychlý Start můžete použít bezplatnou službu.

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Získání koncového bodu HTTP a přístupového klíče" border="false":::

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="create-data-source-skillset-index-and-indexer"></a>Vytváření zdrojů dat, dovednosti, indexů a indexerů

V této části se účtují a poskytnutá kolekce používají k vytvoření zdroje dat služby Search, dovednosti, indexu a indexeru.

1. Pokud tento příspěvek nemáte, můžete [si ho stáhnout tady](https://www.getpostman.com/).
1. [Stažení kolekce po publikování relací ladění](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Spustit post
1. V části **soubory**  >  **nové** vyberte kolekci, kterou chcete importovat.
1. Po importu kolekce rozbalte seznam akce (...).
1. Klikněte na **Upravit**.
1. Zadejte název vašeho searchService (například pokud je koncový bod `https://mydemo.search.windows.net` , název služby je " `mydemo` ").
1. Zadejte apiKey buď s primárním nebo sekundárním klíčem vaší vyhledávací služby.
1. Zadejte storageConnectionString ze stránky klíče účtu Azure Storage.
1. Zadejte ContainerName pro kontejner, který jste vytvořili v účtu úložiště.

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Upravit proměnné v post":::

Kolekce obsahuje čtyři různá volání REST, která se používají k dokončení této části.

První volání vytvoří zdroj dat. `clinical-trials-ds`. Druhé volání vytvoří dovednosti, `clinical-trials-ss` . Třetí volání vytvoří index, `clinical-trials` . Čtvrtý a poslední hovor vytvoří indexer, `clinical-trials-idxr` . Po dokončení všech volání v kolekci zavřete po a vraťte se do Azure Portal.

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="použití metody post pro vytvoření zdroje dat":::

## <a name="check-the-results"></a>Kontrola výsledků

Dovednosti obsahuje několik běžných chyb. V této části spuštěním prázdného dotazu, který vrátí všechny dokumenty, se zobrazí více chyb. V následujících krocích se problémy vyřeší pomocí ladicí relace.

1. V Azure Portal přejít na vyhledávací službu. 
1. Vyberte kartu **indexy** . 
1. Vyberte `clinical-trials` index.
1. Kliknutím na tlačítko **Hledat** spustíte prázdný dotaz. 

Po dokončení hledání se zobrazí dvě pole, která neobsahují žádná data. v okně jsou uvedeny "organizace" a "umístění". Postupujte podle kroků a zjistěte všechny problémy, které jsou vytvořeny nástrojem dovednosti.

1. Vraťte se na stránku Přehled vyhledávací služby.
1. Vyberte kartu **indexery** . 
1. Klikněte `clinical-trials-idxr` a vyberte oznámení upozornění. 

K dispozici je mnoho problémů s mapováním výstupních polí projekce a na stránce tři jsou upozornění, protože jeden nebo více vstupů dovedností je neplatných.

Vraťte se na obrazovku Přehled vyhledávací služby.

## <a name="start-your-debug-session"></a>Spuštění relace ladění

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="spustit novou relaci ladění":::

1. Klikněte na kartu relace ladění (Preview).
1. Vybrat + NewDebugSession
1. Pojmenujte relaci. 
1. Připojte relaci k vašemu účtu úložiště. 
1. Zadejte název indexeru. Indexer má odkazy na zdroj dat, dovednosti a index.
1. Přijměte výchozí volbu dokumentu pro první dokument v kolekci. 
1. **Uložte** relaci. Uložení relace aktivuje kanál obohacení AI podle definice dovednosti.

> [!Important]
> Ladicí relace funguje pouze s jedním dokumentem. Konkrétní dokument v sadě dat je možné > vybrat, jinak se relace nastaví jako výchozí první dokument.

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="Začala nová ladicí relace.":::

Po dokončení relace ladění se ve výchozím nastavení relace karta rozšíření AI zvýrazní a zvýrazní se graf dovedností.

+ Graf dovedností poskytuje vizuální hierarchii dovednosti a pořadí spouštění shora dolů. Dovednosti, které jsou vedle sebe v grafu, se spouštějí paralelně. Barevné kódování dovedností v grafu indikuje typy dovedností, které se spouštějí v dovednosti. V tomto příkladu jsou zelenými dovednostmi text a červená dovednost je vize. Kliknutím na jednotlivé dovednosti v grafu zobrazíte podrobnosti o této instanci dovednosti v pravém podokně okna relace. Nastavení dovedností, Editor JSON, podrobnosti spuštění a chyby nebo upozornění jsou k dispozici ke kontrole a úpravám.
+ Obohacená struktura dat podrobně popisuje uzly ve stromové struktuře rozšíření vygenerované dovednostmi z obsahu zdrojového dokumentu.

Karta chyby a varování poskytne mnohem menší seznam než ten, který se zobrazil dříve, protože tento seznam obsahuje pouze podrobné informace o chybách pro jeden dokument. Podobně jako seznam zobrazený indexerem můžete kliknout na zprávu upozornění a zobrazit podrobnosti tohoto upozornění.

## <a name="fix-missing-skill-input-value"></a>Opravit chybějící vstupní hodnotu dovednosti

Na kartě chyby/upozornění došlo k chybě pro operaci s označením `Enrichment.NerSkillV2.#1` . Podrobnosti o této chybě popisují, že došlo k potížím se vstupní hodnotou dovednosti '/document/languageCode '. 

1. Vraťte se na kartu obohacení AI.
1. Klikněte na **graf dovedností**.
1. Kliknutím na dovednost označený #1 zobrazíte její podrobnosti v pravém podokně.
1. Vyhledejte vstup "languageCode".
1. Vyberte **</>** symbol na začátku řádku a otevřete vyhodnocovací filtr výrazů.
1. Kliknutím na tlačítko **vyhodnocení** potvrďte, že tento výraz má za následek chybu. Ověří, že vlastnost "languageCode" není platný vstup.

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Vyhodnocovač výrazů":::

Existují dva způsoby, jak tuto chybu v relaci prozkoumat. První je podívat se na to, odkud vstup přichází – jakou odbornost v hierarchii by měl tento výsledek vyvolat? Karta spuštění v podokně Podrobnosti dovednosti by měla zobrazit zdroj vstupu. Pokud neexistuje žádný zdroj, znamená to, že došlo k chybě mapování pole.

1. Klikněte na kartu **spuštění** .
1. Podívejte se na vstupy a vyhledejte "languageCode". Pro tento vstup není uveden žádný zdroj. 
1. Přepněte v levém podokně, aby se zobrazila obohacená datová struktura. Neexistuje žádná mapovaná cesta odpovídající "languageCode".

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Obohacená datová struktura":::

Pro jazyk je namapovaná cesta. V nastavení dovedností proto dojde k překlepu. Chcete-li tento výraz opravit, je nutné aktualizovat výraz v #1 dovednost s výrazem '/Document/Language '.

1. Otevřete vyhodnocovací filtr výrazů **</>** pro jazykovou cestu.
1. Zkopírujte výraz. Zavřete okno.
1. Přejít na nastavení dovedností pro #1 dovednost a otevřete vyhodnocovací filtr výrazů **</>** pro vstup "languageCode".
1. Do pole výraz vložte novou hodnotu/Document/Language a klikněte na **vyhodnotit**.
1. Měl by se zobrazit správný vstup "en". Pokud chcete výraz aktualizovat, klikněte na použít.
1. Klikněte na **Uložit** v podokně Podrobnosti o dovednostech vpravo.
1. V nabídce okna relace klikněte na **Spustit** . Tím se zahájí další spuštění dovednosti s použitím dokumentu. 

Po dokončení spuštění relace ladění klikněte na kartu chyby/upozornění a zobrazí se zpráva s označením "obohacení. NerSkillV2. #1". Nicméně stále existují dvě upozornění, která by služba nemohla mapovat výstupní pole pro organizace a umístění do indexu vyhledávání. Chybí hodnoty: '/Document/merged_content/Organizations ' a '/Document/merged_content/Locations '.

## <a name="fix-missing-skill-output-values"></a>Oprava chybějících výstupních hodnot dovedností

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Chyby a upozornění":::

Z dovednosti chybí výstupní hodnoty. Chcete-li identifikovat dovednost s chybou přejít do rozšířené datové struktury, vyhledejte název hodnoty a podívejte se na jeho původní zdroj. V případě chybějících organizací a hodnot umístění se jedná o výstupy #1ch dovedností. Otevření vyhodnocovacího filtru výrazů </> pro každou cestu zobrazí výrazy uvedené jako "/Document/Content/Organizations" a "/Document/Content/Locations" (v uvedeném pořadí).

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Entita vyhodnocení výrazu – entita organizace":::

Výstup těchto entit je prázdný a neměl by být prázdný. Jaké jsou vstupy vytvářející tento výsledek?

1. Přejít do **grafu dovednosti** a vyberte dovednost #1.
1. V pravém podokně podrobností dovednosti vyberte kartu **spuštění** .
1. Otevřete vyhodnocovací filtr výrazů **</>** pro vstup "text".

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Vstup pro textovou dovednost":::

Zobrazený výsledek tohoto vstupu nevypadá jako textový vstup. Vypadá jako obrázek, který je ohraničen novými řádky. Nedostatek textu znamená, že není možné identifikovat žádné entity. V hierarchii dovednosti se zobrazuje obsah nejprve zpracován dovedností #6 (OCR) a pak se předává do dovednosti #5 (sloučení). 

1. Vyberte dovednost #5 (sloučit) v **grafu dovedností**.
1. Vyberte kartu **spuštění** v pravém podokně podrobností o dovednostech a otevřete vyhodnocovací filtr výrazů **</>** pro výstupy "mergedText".

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Výstup pro dovednost sloučení":::

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

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Výstup pro entitu organizace":::

Vyhodnocení výsledku výrazu dává správný výsledek. Dovednost pracuje na určení správné hodnoty pro entitu "organizace". Mapování výstupu v cestě entity však stále vyvolává chybu. Při porovnávání výstupní cesty v dovednosti s výstupní cestou v této chybě se jedná o dovednosti, které jsou nadřazené výstupům, organizacím a umístěním pod uzlem/Document/Content. Mapování polí pro výstup očekává, že výsledky budou nadřazené v uzlu/Document/merged_content. V předchozím kroku se vstup změnil z '/Document/Content ' na '/Document/merged_content '. Kontext v nastavení dovedností se musí změnit, aby se zajistilo, že se výstup vygeneruje pomocí správného kontextu.

1. Vyberte kartu **rozšíření AI** .
1. Vyberte **graf dovedností** a klikněte na #1 dovednosti.
1. Přejděte na **Nastavení dovedností** a vyhledejte "Context".
1. Poklikejte na nastavení "Context" a upravte ho pro čtení/Document/merged_content.
1. Klikněte na **Uložit** v podokně Podrobnosti o dovednostech vpravo.
1. V nabídce okna relace klikněte na **Spustit** . Tím se zahájí další spuštění dovednosti s použitím dokumentu.

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Oprava kontextu v nastavení dovedností":::

Všechny chyby byly vyřešeny.

## <a name="commit-changes-to-the-skillset"></a>Potvrdit změny do dovednosti

Po zahájení relace ladění vytvořila vyhledávací služba kopii dovednosti. To bylo provedeno, takže provedené změny neovlivní produkční systém. Teď, když jste dokončili ladění vašeho dovednostiu, je možné opravy potvrdit (přepsat původní dovednosti) do produkčního systému. Pokud chcete pokračovat v provádění změn v dovednosti, aniž by to ovlivnilo produkční systém, můžete relaci ladění Uložit a znovu otevřít později.

1. V nabídce hlavní relace ladění klikněte na **Potvrdit změny** .
1. Kliknutím na **OK** potvrďte, že chcete aktualizovat dovednosti.
1. Zavřete relaci ladění a vyberte kartu **indexery** .
1. Otevřete okno klinická hodnocení – idxr.
1. Klikněte na **resetovat**.
1. Klikněte na **Spustit**. Potvrďte kliknutím na tlačítko **OK** .

Po skončení běhu indexeru by mělo být zelené zaškrtnutí a slovo úspěšné vedle časového razítka posledního spuštění na kartě Historie spuštění. Chcete-li zajistit, že byly provedeny změny:

1. Ukončete **indexer** a vyberte kartu **index** .
1. Otevřete index klinických pokusů a na kartě Průzkumník vyhledávání klikněte na **Hledat**.
1. V okně výsledků by se mělo zobrazit, že entity a umístění jsou nyní vyplněny očekávanými hodnotami.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o dovednosti](./cognitive-search-working-with-skillsets.md) 
>  [Další informace o přírůstkovém obohacení a ukládání do mezipaměti](./cognitive-search-incremental-indexing-conceptual.md)