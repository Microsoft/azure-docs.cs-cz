---
title: Tipy pro návrh rozšíření AI
titleSuffix: Azure Cognitive Search
description: Tipy a řešení potíží při nastavování kanálů obohacení AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 1ab366cddbabf7e6d574189892e779ab49f6fad8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403377"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tipy pro rozšíření AI v Azure Kognitivní hledání

Tento článek obsahuje seznam tipů a triky, které vám pomohou při přesunu při zahájení práce s funkcemi obohacení AI v Azure Kognitivní hledání. 

Pokud jste to ještě neudělali, Projděte si postup v tomto [kurzu: Naučte se volat rozhraní API pro obohacení](cognitive-search-quickstart-blob.md) AI pro použití rozšíření AI na zdroj dat objektu BLOB.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: Začněte s malou datovou sadou
Nejlepším způsobem, jak rychle najít problémy, je zvýšit rychlost, s jakou můžete problémy opravovat. Nejlepším způsobem, jak zkrátit dobu indexování, je snížit počet dokumentů, které mají být indexovány. 

Začněte tím, že vytvoříte zdroj dat s pouze několik dokumentů nebo záznamů. Ukázka dokumentu by měla být dobrá reprezentace nejrůznějších dokumentů, které budou indexovány. 

Spusťte ukázku dokumentu prostřednictvím koncového kanálu a ověřte, že výsledky vyhovují vašim potřebám. Až budete s výsledky spokojeni, můžete do zdroje dat přidat další soubory.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Ujistěte se, že jsou přihlašovací údaje ke zdroji dat správné.
Připojení ke zdroji dat není ověřeno, dokud nedefinujete indexer, který ho používá. Pokud se zobrazí chyby, které zmiňuje, že se indexer nemůže dostat k datům, ujistěte se, že:
- Připojovací řetězec je správný. Speciálně při vytváření tokenů SAS se ujistěte, že používáte formát očekávaný službou Azure Kognitivní hledání. Další informace o podporovaných formátech najdete v [části určení přihlašovacích údajů](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) .
- Název kontejneru v indexeru je správný.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: Podívejte se, jak funguje, i když dojde k nějakým chybám.
Někdy malé selhání zastaví indexer ve svých stopách. To je dobré, pokud plánujete opravit problémy jednu po jedné. Můžete ale chtít ignorovat konkrétní typ chyby, aby indexer mohl pokračovat, abyste viděli, jaké toky skutečně fungují.

V takovém případě může být vhodné říct indexeru, aby ignoroval chyby. Nastavení *maxFailedItems* a *maxFailedItemsPerBatch* jako-1 v rámci definice indexeru.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
> [!NOTE]
> Osvědčeným postupem je nastavit maxFailedItems, maxFailedItemsPerBatch na 0 pro produkční úlohy.

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>Tip 4: použití relací ladění k identifikaci a řešení problémů s dovednosti 

Relace ladění je vizuální editor, který pracuje s existujícím dovednosti v Azure Portal. V rámci ladicí relace můžete identifikovat a vyřešit chyby, ověřovat změny a potvrzovat změny v produkčním dovednosti v kanálu pro rozšíření AI. Tato funkce verze Preview [si přečte dokumentaci](./cognitive-search-debug-session.md). Další informace o konceptech a o tom, jak začít, najdete v tématu [relace ladění](./cognitive-search-tutorial-debug-sessions.md).

Relace ladění fungují na jednom dokumentu, což je skvělý způsob, jak iterativním sestavovat složitější kanály pro obohacení.

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>Tip 5: prohlížení obohacených dokumentů v digestoři 
Obohacené dokumenty jsou dočasné struktury vytvořené během obohacení a po dokončení zpracování se odstraní.

Pokud chcete zachytit snímek rozšířeného dokumentu vytvořeného během indexování, přidejte do indexu pole s názvem ```enriched```. Indexer do tohoto pole automaticky vypíše řetězcovou reprezentaci všech rozšíření daného dokumentu.

Pole ```enriched``` bude obsahovat řetězec, který je logickou reprezentací rozšířeného dokumentu uloženého v paměti ve formátu JSON.  Hodnota pole je ale platný dokument JSON. Abyste si mohli dokument zobrazit jako formátovaný dokument JSON, bude nutné nahradit `\"` za `"`, protože pro uvozovky se používají řídicí sekvence. 

Obohacené pole je určeno pouze pro účely ladění a pomůže vám pochopit logický tvar obsahu, proti kterému jsou výrazy vyhodnocovány. Pro účely indexování byste neměli záviset na tomto poli.

Přidejte ```enriched``` pole jako součást definice indexu pro účely ladění:

#### <a name="request-body-syntax"></a>Syntaxe textu požadavku
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-6-expected-content-fails-to-appear"></a>Tip 6: neúspěšné zobrazení očekávaného obsahu

Chybějící obsah by mohl být výsledkem vynechání dokumentů při indexování. Úrovně Free a Basic mají nízká omezení velikosti dokumentu. Při indexování se vynechává libovolný soubor překračující limit. Odhozené dokumenty můžete vyhledat v Azure Portal. Na řídicím panelu služby Search poklikejte na dlaždici indexery. Zkontrolujte poměr úspěšných indexovaných dokumentů. Pokud není 100%, můžete kliknutím na poměr získat další podrobnosti. 

Pokud se problém týká velikosti souboru, může se zobrazit chyba, například objekt BLOB \<file-name> má velikost \<file-size> bajtů, což překračuje maximální velikost pro extrakci dokumentů pro aktuální úroveň služby. Další informace o omezeních indexeru najdete v tématu [omezení služby](search-limits-quotas-capacity.md).

Druhý důvod pro neúspěšné zobrazení obsahu může souviset s chybami mapování vstupu a výstupu. Například název cíle výstupu je "lidé", ale název pole indexu je malými písmeny "lidé". Systém může vracet 201 zpráv o úspěchu pro celý kanál, takže si myslíte, že indexování bylo úspěšné, když je ve skutečnosti pole prázdné. 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 7: rozšířené zpracování více než maximální doba běhu (24 hodinové okno)

Analýza obrázků je poměrně náročná pro dokonce i jednoduché případy, takže pokud jsou obrázky obzvláště velké nebo složité, doba zpracování může překročit maximální povolený čas. 

Maximální doba běhu se liší podle úrovně: několik minut na úrovni Free, ve 24hodinovém indexování na fakturovatelných úrovních. Pokud se zpracování nepovede do 24 hodin po zpracování na vyžádání, přepněte na plán, aby indexovací služba vybrala zpracování tam, kde skončila. 

V případě naplánovaných indexerů se indexování obnoví podle plánu v posledním známém dobrém dokumentu. Pomocí opakovaného plánu může indexer pracovat svým způsobem prostřednictvím nevyřízených položek imagí v rámci série hodin nebo dnů, dokud nebudou zpracovány všechny nezpracované image. Další informace o syntaxi plánu najdete v [kroku 3: Create-a-indexer](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) nebo v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

> [!NOTE]
> Pokud je indexer nastavený na určitý plán, ale opakovaně selže na stejném dokumentu a znovu se pokaždé, když se spustí, indexer začne běžet v kratším intervalu (až do maximálního počtu alespoň každých 24 hodin), dokud to znovu neudělá.  Pokud se domníváte, že jste se rozhodli, že jste si myslíte, že byl problém, který způsobil, že se indexer zablokoval v určitém bodě, můžete na vyžádání provést indexer a pokud se to úspěšně dokončí, indexer se znovu vrátí do intervalu nastaveného plánu.

V případě indexování na základě portálu (jak je popsáno v rychlém startu) výběr možnosti indexer "spustit jednou" omezuje zpracování na 1 hodinu ( `"maxRunTime": "PT1H"` ). Je možné, že budete chtít okno zpracování prodloužit na nějakou dobu.

## <a name="tip-8-increase-indexing-throughput"></a>Tip 8: zvýšení propustnosti indexování

V případě [paralelního indexování](search-howto-large-index.md)umístěte data do více kontejnerů nebo více virtuálních složek uvnitř stejného kontejneru. Pak vytvořte více datových zdrojů a párů indexerů. Všechny indexery můžou používat stejný dovednosti a zapisovat do stejného cílového vyhledávacího indexu, takže si vaše vyhledávací aplikace nemusí být vědomá tohoto dělení.

## <a name="see-also"></a>Viz také

+ [Rychlý Start: vytvoření kanálu pro rozšíření AI na portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Seznámení s rozhraními REST API pro obohacení AI](cognitive-search-tutorial-blob.md)
+ [Zadání přihlašovacích údajů ke zdroji dat](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Mapování obohacených polí na index](cognitive-search-output-field-mapping.md)