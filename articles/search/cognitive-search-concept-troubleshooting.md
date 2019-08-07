---
title: Tipy pro řešení potíží pro rozpoznávání rozpoznávání – Azure Search
description: Tipy a řešení potíží při nastavování kanálů hledání rozpoznávání v Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.openlocfilehash: b85fb5417b014041024dd83ca62572282c6edf81
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841271"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Tipy pro řešení potíží s vyhledáváním rozpoznávání

Tento článek obsahuje seznam tipů a triky, které vám pomohou při práci s funkcemi pro vyhledávání rozpoznávání v Azure Search. 

Pokud jste to ještě neudělali, Projděte si [postup v tomto kurzu: Naučte se volat rozhraní API](cognitive-search-quickstart-blob.md) pro hledání vnímání pro postupy při použití rozšíření rozpoznávání vyhledávání pro zdroj dat objektu BLOB.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: Začínáme s malou datovou sadou
Nejlepším způsobem, jak rychle najít problémy, je zvýšit rychlost, s jakou můžete problémy opravovat. Nejlepším způsobem, jak zkrátit dobu indexování, je snížit počet dokumentů, které mají být indexovány. 

Začněte tím, že vytvoříte zdroj dat s pouze několik dokumentů nebo záznamů. Ukázka dokumentu by měla být dobrá reprezentace nejrůznějších dokumentů, které budou indexovány. 

Spusťte ukázku dokumentu prostřednictvím koncového kanálu a ověřte, že výsledky vyhovují vašim potřebám. Až budete s výsledky spokojeni, můžete do zdroje dat přidat další soubory.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Ujistěte se, že přihlašovací údaje ke zdroji dat jsou správné.
Připojení ke zdroji dat není ověřeno, dokud nedefinujete indexer, který ho používá. Pokud se zobrazí chyby, které zmiňuje, že se indexer nemůže dostat k datům, ujistěte se, že:
- Připojovací řetězec je správný. Speciálně při vytváření tokenů SAS je nutné použít formát očekávaný Azure Search. Další [informace o podporovaných formátech najdete](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) v části určení přihlašovacích údajů.
- Název kontejneru v indexeru je správný.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: Podívejte se, co funguje i v případě selhání.
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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tip 4: Prohlížení obohacených dokumentů v digestoři 
Obohacené dokumenty jsou dočasné struktury vytvořené během obohacení a po dokončení zpracování se odstraní.

Pokud chcete zachytit snímek rozšířeného dokumentu vytvořeného během indexování, přidejte do indexu pole s názvem ```enriched```. Indexer do tohoto pole automaticky vypíše řetězcovou reprezentaci všech rozšíření daného dokumentu.

Pole ```enriched``` bude obsahovat řetězec, který je logickou reprezentací rozšířeného dokumentu uloženého v paměti ve formátu JSON.  Hodnota pole je ale platný dokument JSON. Abyste si mohli dokument zobrazit jako formátovaný dokument JSON, bude nutné nahradit `\"` za `"`, protože pro uvozovky se používají řídicí sekvence. 

Obohacené pole je určeno pouze pro účely ladění a pomůže vám pochopit logický tvar obsahu, proti kterému jsou výrazy vyhodnocovány. Pro účely indexování byste neměli záviset na tomto poli.

```enriched``` Přidejte pole jako součást definice indexu pro účely ladění:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Tip 5: Neúspěšné zobrazení očekávaného obsahu

Chybějící obsah by mohl být výsledkem vynechání dokumentů při indexování. Úrovně Free a Basic mají nízká omezení velikosti dokumentu. Při indexování se vynechává libovolný soubor překračující limit. Odhozené dokumenty můžete vyhledat v Azure Portal. Na řídicím panelu služby Search poklikejte na dlaždici indexery. Zkontrolujte poměr úspěšných indexovaných dokumentů. Pokud není 100%, můžete kliknutím na poměr získat další podrobnosti. 

Pokud se problém týká velikosti souboru, může se zobrazit chyba podobná této: "Název souboru \<objektu BLOB >" má \<velikost souborů > bajtů, což překračuje maximální velikost pro extrakci dokumentů pro aktuální úroveň služby. " Další informace o omezeních indexeru najdete v tématu [omezení služby](search-limits-quotas-capacity.md).

Druhý důvod pro neúspěšné zobrazení obsahu může souviset s chybami mapování vstupu a výstupu. Například název cíle výstupu je "lidé", ale název pole indexu je malými písmeny "lidé". Systém může vracet 201 zpráv o úspěchu pro celý kanál, takže si myslíte, že indexování bylo úspěšné, když je ve skutečnosti pole prázdné. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 6: Rozšířené zpracování více než maximální doba běhu (24 hodinové okno)

Analýza obrázků je poměrně náročná pro dokonce i jednoduché případy, takže pokud jsou obrázky obzvláště velké nebo složité, doba zpracování může překročit maximální povolený čas. 

Maximální doba běhu se liší podle úrovně: několik minut na úrovni Free, ve 24hodinovém indexování na fakturovatelných úrovních. Pokud se zpracování nepovede do 24 hodin po zpracování na vyžádání, přepněte na plán, aby indexovací služba vybrala zpracování tam, kde skončila. 

V případě naplánovaných indexerů se indexování obnoví podle plánu v posledním známém dobrém dokumentu. Pomocí opakovaného plánu může indexer pracovat svým způsobem prostřednictvím nevyřízených položek imagí v rámci série hodin nebo dnů, dokud nebudou zpracovány všechny nezpracované image. Další informace o syntaxi plánu najdete v [kroku 3: Vytvořte indexer](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) nebo si přečtěte, [jak naplánovat indexery pro Azure Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Pokud je indexer nastavený na určitý plán, ale opakovaně selže na stejném dokumentu a znovu se pokaždé, když se spustí, indexer začne běžet v kratším intervalu (až do maximálního počtu alespoň každých 24 hodin), dokud úspěšně nedosáhne průběhu AGA pro.  Pokud se domníváte, že jste se rozhodli, že jste si myslíte, že byl problém, který způsobil, že se indexer zablokoval v určitém bodě, můžete na vyžádání provést indexer a pokud se to úspěšně dokončí, indexer se znovu vrátí do intervalu nastaveného plánu.

V případě indexování na základě portálu (jak je popsáno v rychlém startu) výběr možnosti indexer "spustit jednou" omezuje zpracování na 1 hodinu (`"maxRunTime": "PT1H"`). Je možné, že budete chtít okno zpracování prodloužit na nějakou dobu.

## <a name="tip-7-increase-indexing-throughput"></a>Tip 7: Zvýšit propustnost indexování

V případě [paralelního indexování](search-howto-large-index.md)umístěte data do více kontejnerů nebo více virtuálních složek uvnitř stejného kontejneru. Pak vytvořte více datových zdrojů a párů indexerů. Všechny indexery můžou používat stejný dovednosti a zapisovat do stejného cílového vyhledávacího indexu, takže si vaše vyhledávací aplikace nemusí být vědomá tohoto dělení.
Další informace najdete v tématu [indexování velkých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Viz také:
+ [Rychlé zprovoznění: Vytvoření kanálu pro hledání vnímání na portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Přečtěte si rozhraní REST API pro vyhledávání rozpoznávání](cognitive-search-tutorial-blob.md)
+ [Zadání přihlašovacích údajů ke zdroji dat](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexování velkých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Mapování obohacených polí na index](cognitive-search-output-field-mapping.md)
