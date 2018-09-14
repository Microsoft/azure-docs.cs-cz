---
title: Poradce při potížích pro kognitivního vyhledávání ve službě Azure Search | Dokumentace Microsoftu
description: Tipy a řešení potíží s nastavením cognitive hledání kanálů ve službě Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 3d0d468674a2284e9925c1410f2bb8bcbde8f73f
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575291"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Tipy pro řešení potíží pro kognitivního vyhledávání

Tento článek obsahuje seznam tipy a triky pro vás přechod jak asi začít pracovat s funkce kognitivního vyhledávání ve službě Azure Search. 

Pokud jste tak ještě neučinili, krokovat [kurz: Zjistěte, jak volat rozhraní API pro kognitivní vyhledávání](cognitive-search-quickstart-blob.md) pro přístup k použití obohacení kognitivního vyhledávání pro zdroj dat objektu blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: Začněte s malou datovou sadu
Nejlepší způsob, jak rychle najít problémy je zvýšit rychlost, jakou můžou opravit problémy. Nejlepší způsob, jak zkrátit čas, který indexování se snížením počtu indexovaných dokumentů. 

Začněte vytvořením zdroje dat s pouze několika dokumenty nebo záznamy. Ukázky dokumentu by měl být reprezentaci různých dokumenty, které bude možné indexovat. 

Spusťte ukázku dokumentu prostřednictvím kanálu začátku do konce a podívejte se, že výsledky splňují vaše potřeby. Jakmile budete spokojeni s výsledky, můžete přidat další soubory ke zdroji dat.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Ujistěte se, že vaše přihlašovací údaje ke zdroji dat jsou správné
Připojení ke zdroji dat neověří, dokud definování indexeru, která ji používá. Pokud uvidíte nějaké chyby, uvedení, že indexer nelze získat k datům, ujistěte se, že:
- Připojovací řetězec uveden správně. Speciálně při vytváření tokenů SAS, ujistěte se, že chcete použít formát očekávaného Azure Search. Zobrazit [jak zadat přihlašovací údaje části](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) Další informace o různých formátů podporovaných.
- Název kontejneru v indexeru je správná.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: Podívejte se, co funguje i v případě, že tam jsou nějaké chyby
Někdy malý selhání zastaví indexer v svoje stopy. To je v pořádku, pokud chcete opravit problémy jeden po druhém. Však můžete chtít ignorovat konkrétní typ chyby, což indexer pokračujte tak, abyste viděli, co jsou toky skutečně funguje.

V takovém případě můžete chtít zjistit indexeru ignorovat chyby. To udělat tak, že nastavíte *maxFailedItems* a *maxFailedItemsPerBatch* jako hodnotu -1 jako součást definice indexeru.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tip 4: Prohlížení bohatších možností dokumenty pohled pod kapotu 
Bohatších možností dokumenty jsou dočasné struktury vytvoří během rozšiřování a poté odstraní po dokončení zpracování.

Pokud chcete zachytit snímek rozšířeného dokumentu vytvořeného během indexování, přidejte do indexu pole s názvem ```enriched```. Indexer do tohoto pole automaticky vypíše řetězcovou reprezentaci všech rozšíření daného dokumentu.

Pole ```enriched``` bude obsahovat řetězec, který je logickou reprezentací rozšířeného dokumentu uloženého v paměti ve formátu JSON.  Hodnota pole je ale platný dokument JSON. Abyste si mohli dokument zobrazit jako formátovaný dokument JSON, bude nutné nahradit `\"` za `"`, protože pro uvozovky se používají řídicí sekvence. 

Pole bohatších možností je určená pro účely pouze, které vám pomohou pochopit logický tvar obsah, který jsou výrazy vyhodnocovány proti ladění. Nemělo záviset toto pole pro účely indexování.

Přidat ```enriched``` pole jako součást definice indexu pro účely ladění:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Tip 5: Byl očekáván obsah nebude moci zobrazit

Chybějící obsah může být způsobeno získávání vyřadit během indexování dokumentů. Úrovně Free a Basic mají nízké omezení velikosti dokumentu. Všechny soubory při překročení tohoto limitu je vyřazeno během indexování. Můžete vyhledat vyřazené dokumenty na webu Azure Portal. V řídicím panelu služby search service dvakrát klikněte na dlaždici indexery. Projděte si poměr úspěšné dokumenty indexované. Pokud není 100 %, můžete kliknout na poměr, abyste získali více podrobností. 

Pokud problém souvisí s velikost souboru, může se zobrazit chyba takto: "Blob < název_souboru >" má velikost < velikost souboru > bajtů, což překračuje maximální velikost pro extrakci dokument pro vaši aktuální úroveň služby. " Další informace o limitech indexer, naleznete v tématu [omezení služby](search-limits-quotas-capacity.md).

Druhý důvod selhání se zobrazí obsah může dojít k chybám související vstupní a výstupní mapování. Například cílový název výstupního je "Lidé", ale název pole indexu je malá "osob". Systém může vrátit 201 zpráv o úspěchu operací pro celý kanál, takže si myslíte, že indexování úspěšné, když ve skutečnosti je pole prázdné. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 6: Rozšíření zpracování překračuje maximum běhu (období 24 hodin)

Analýza obrázku je výpočetně náročné i jednoduché v případech, tak při bitové kopie jsou obzvláště rozsáhlá nebo složitá, časy zpracování může být delší než maximální dobu povolenou. 

Maximální doba spuštění se liší podle úrovně: několik minut na Free vrstvy, 24 hodin indexování fakturovatelné vrstev. Pokud se zpracování nepodaří dokončit během 24 hodin pro zpracování na vyžádání, přepněte na plán, který chcete nechat pokračovat zpracování tam, kde skončila. 

Pro plánované indexery indexování obnoví podle plánu na poslední známé dobré dokumentu. Pomocí opakovaně indexeru můžete fungují jeho prostřednictvím nevyřízených položek image několik hodin nebo dnů, dokud se zpracovávají všechny bitové kopie zrušení zpracování. Další informace o plánu syntaxe, naleznete v tématu [krok 3: vytvoření indexeru](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Založené na portálu indexování (jak je popsáno v tomto rychlém startu), výběr indexer "Spustit jednou" možnost omezení zpracování na 1 hodinu (`"maxRunTime": "PT1H"`). Můžete chtít rozšířit okno zpracování na něco delší dobu.

## <a name="tip-7-increase-indexing-throughput"></a>Tip 7: Zvýšit propustnost indexování

Pro [paralelní indexování](search-howto-large-index.md), uložit svá data do několika kontejnerů nebo více virtuální složky uvnitř stejného kontejneru. Vytvořte více párů zdroj dat a indexeru. Všechny indexery, můžete použít stejné dovednosti a zapisovat do stejného cílovým indexem vyhledávání tak vaše vyhledávací aplikace nemusí mít na paměti tato dělení.
Další informace najdete v tématu [indexování velkých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Další informace najdete v tématech
+ [Rychlý start: Vytvoření kanálu kognitivního vyhledávání v portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Přečtěte si kognitivního vyhledávání rozhraní REST API](cognitive-search-tutorial-blob.md)
+ [Zadání přihlašovacích údajů zdroje dat](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexování velkých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Jak namapovat bohatších možností pole indexu](cognitive-search-output-field-mapping.md)