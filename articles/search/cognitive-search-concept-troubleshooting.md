---
title: Řešení potíží s tipy pro kognitivní vyhledávání ve službě Azure Search | Microsoft Docs
description: Tipy a řešení potíží pro nastavení kognitivní vyhledávání ve službě Azure Search kanály.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 15fc879958bfd886210a90239e0247c60fe231f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Tipy k řešení potíží pro kognitivní vyhledávání

Tento článek obsahuje seznam tipy a triky účelem udržování přesunutí, jak začít pracovat s možnosti kognitivní vyhledávání ve službě Azure Search. 

Pokud jste tak ještě neučinili, jednotlivé kroky [kurz: Zjistěte, jak volat rozhraní API kognitivní hledání](cognitive-search-quickstart-blob.md) pro praxi v použití kognitivní vyhledávání enrichments ke zdroji dat objektů blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: Začínat na malou datovou sadu
Nejlepší způsob, jak rychle najít problémy je zvýšit rychlost, jakou můžete je vyřešit problémy. Snížením počtu dokumentů indexovaných je nejlepší způsob, jak snížit čas indexování. 

Začněte vytvořením zdroje dat s jen několik záznamů, nebo dokumenty. Ukázky dokumentu by měla být funkční reprezentace řadu dokumenty, které bude možné indexovat. 

Spuštění ukázky dokumentu prostřednictvím kanálu začátku do konce a zkontrolujte, že výsledky podle svých potřeb. Až budete s výsledky spokojeni, můžete přidat další soubory na svůj zdroj dat.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Ověřte, že jsou správné přihlašovací údaje zdroje dat.
Připojení ke zdroji dat není ověřen, dokud definujete indexer, který se používá. Pokud se zobrazí chyby zmínit, že indexeru nelze získat k datům, ujistěte se, že:
- Připojovací řetězec je správná. Speciálně, při vytváření Tokeny SAS, ujistěte se, že jste použijte formát očekávanou Azure Search. V tématu [určení pověření části](
https://docs.microsoft.com/en-us/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) Další informace o různých formátech podporována.
- Název kontejneru v indexeru je správná.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: Najdete v části co funguje, i když některé chyby
Někdy k malé chybě zastaví indexer v jeho sleduje. Který je v pořádku, pokud chcete opravit problémy, po jednom. Ale můžete chtít ignorovat konkrétní typ chyby, což indexeru pokračujte tak, aby se zobrazí, co jsou toky skutečně funguje.

V takovém případě můžete zjistit indexeru ignorování chyb. Udělat nastavením *maxFailedItems* a *maxFailedItemsPerBatch* hodnotu -1 jako součást definice indexer.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tip 4: Prohlížení provádět rozšířené dokumenty pod pokličkou 
Provádět rozšířené dokumenty jsou dočasné struktury vytvořené během obohacení a poté odstranit po dokončení zpracování.

Když Pokud chcete zachytit snímek provádět rozšířené dokument vytvořený během indexování, přidat pole s názvem ```enriched``` do indexu. Do pole indexeru automaticky vypíše řetězcovou reprezentaci všech enrichments pro tento dokument.

```enriched``` Pole bude obsahovat řetězec, který je logická reprezentace provádět rozšířené dokumentu v paměti ve formátu JSON.  Hodnota pole je ale na platný dokument JSON. Uvozovky jsou uvozeny uvozovacím znakem, budete muset nahradit `\"` s `"` Chcete-li zobrazit dokumentu jako formátu JSON. 

Pole provádět rozšířené je určený pro účely, vám pomohou pochopit logický obrazec obsah, který se se vyhodnotí výrazy proti ladění. Neměli závisí na toto pole pro účely indexování.

Přidat ```enriched``` pole jako součást definice indexu pro účely ladění:

#### <a name="request-body-syntax"></a>Syntaxe požadavku textu
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

## <a name="tip-5-expected-content-fails-to-appear"></a>Tip 5: Očekávaný obsah selže zobrazit

Chybějící obsah může být způsobeno získávání vyřadit během indexování dokumentů. Bezplatnými a základní úrovněmi mají nízkou limity velikosti dokumentu. Jakýkoli soubor překračuje limit je vyřazeno během indexování. Můžete zkontrolovat pro vyřazené dokumenty na portálu Azure. V řídicím panelu služby vyhledávání dvakrát klikněte na dlaždici indexery. Zkontrolujte poměr úspěšné dokumentů indexovaných. Pokud není 100 %, můžete kliknout na poměr získat další podrobnosti. 

Pokud tento problém souvisí s velikost souboru, může se zobrazit chyba takto: má velikost < velikost souboru > bajtů, která přesahuje maximální velikost pro extrakci dokumentu pro vaše aktuální úroveň služby "Blob < název souboru >". " Další informace o omezení indexer najdete v tématu [omezení služby](search-limits-quotas-capacity.md).

Druhý důvod selhání zobrazí obsah může být související mapování vstupně výstupní chyby. Například název výstupního cíl je "Lidé", ale název pole indexu je malá "lidé". Systém může vrátit 201 úspěch zprávy pro celý kanál, tak si myslíte, že indexování úspěšné, když ve skutečnosti je pole prázdné. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 6: Rozšiřte zpracování nad maximální běh (24 hodin)

Analýza bitové kopie je výpočetně náročných i jednoduché v případech, takže pokud Image jsou obzvláště velká nebo složitá, časy zpracování nesmí překročit maximální časový limit. 

Maximální doba běhu se liší podle úrovně: několik minut na volné vrstvy, indexování v fakturovatelný vrstvách 24 hodin. Pokud zpracování se nepodaří dokončit během období 24 hodin pro zpracování na vyžádání, přepněte na plán, který chcete mít indexeru vyzvednutí zpracování, kde bylo přerušeno. 

Naplánované indexery indexování obnoví podle plánu na poslední známé funkční dokumentu. Pomocí plánu opakování, můžete indexeru pracovat skrze nevyřízené položky bitové kopie v řadě hodin nebo dnů, dokud nebude zpracování všech zrušení zpracování obrázků. Další informace o plánu syntaxi najdete v tématu [krok 3: vytvoření indexer](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Pro založené na portálu indexování (jak je popsáno v rychlý start), výběr indexeru "Spustit jednou" možnost omezení zpracování na 1 hodinu (`"maxRunTime": "PT1H"`). Můžete chtít rozšířit okno zpracování na něco delší.

## <a name="tip-7-increase-indexing-throughput"></a>Tip 7: Zvýšit propustnost indexování

Pro [paralelní indexování](search-howto-reindex.md#parallel-indexing), umístit data do několika kontejnerů nebo více virtuálních složek ve stejném kontejneru. Pak vytvořte více párů zdroj dat a indexeru. Všechny indexery, můžete použít stejné skillset a zapisovat do stejné cílový index vyhledávání, hledání aplikace nemusí mít na paměti Toto rozdělení do oddílů.
Další informace najdete v tématu [indexování rozsáhlých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Další informace najdete v tématech
+ [Rychlý úvod: Vytvoření kanálu kognitivní vyhledávání v portálu.](cognitive-search-quickstart-blob.md)
+ [Kurz: Další kognitivní vyhledávání rozhraní REST API](cognitive-search-tutorial-blob.md)
+ [Zadání přihlašovacích údajů zdroje dat](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexování rozsáhlých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Postup mapování na index provádět rozšířené pole](cognitive-search-output-field-mapping.md)