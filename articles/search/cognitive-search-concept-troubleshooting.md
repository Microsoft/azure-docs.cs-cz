---
title: Tipy pro design obohacení umělou ai.
titleSuffix: Azure Cognitive Search
description: Tipy a řešení potíží pro nastavení kanálů obohacení umělou ai ve službě Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245483"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tipy pro obohacení umělou aivem v Azure Cognitive Search

Tento článek obsahuje seznam tipů a triků, které vám pomohou v pohybu, až začnete s možnostmi obohacení umělá inteligence v Azure Cognitive Search. 

Pokud jste tak ještě neučinili, krok ovat [kurz: Naučte se volat api obohacení AI](cognitive-search-quickstart-blob.md) pro praxi při použití obohacení AI na zdroj dat objektu blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: Začněte s malou datovou sadou
Nejlepší způsob, jak rychle najít problémy, je zvýšit rychlost, s jakou můžete problémy opravit. Nejlepší způsob, jak zkrátit dobu indexování, je snížení počtu dokumentů, které mají být indexovány. 

Začněte vytvořením zdroje dat s několika dokumenty/záznamy. Ukázka dokumentu by měla být dobrou reprezentací různých dokumentů, které budou indexovány. 

Spusťte ukázku dokumentu prostřednictvím kanálu od konce a zkontrolujte, zda výsledky vyhovují vašim potřebám. Jakmile budete s výsledky spokojeni, můžete do zdroje dat přidat další soubory.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Zkontrolujte správná pověření zdroje dat.
Připojení zdroje dat není ověřeno, dokud nedefinujete indexer, který jej používá. Pokud se zobrazí chyby, které zmiňují, že indexer nemůže získat data, ujistěte se, že:
- Připojovací řetězec je správný. Speciálně při vytváření tokenů SAS, ujistěte se, že použití formátu očekávaného Azure Cognitive Search. Informace o různých podporovaných formátech naleznete v [části Jak zadat přihlašovací údaje.](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials)
- Název kontejneru v indexeru je správný.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: Podívejte se, co funguje, i když tam jsou některé poruchy
Někdy malé selhání zastaví indexer v jeho stopy. To je v pořádku, pokud máte v plánu opravit problémy jeden po druhém. Můžete však chtít ignorovat určitý typ chyby, což umožňuje indexeru pokračovat, takže můžete vidět, jaké toky jsou skutečně funkční.

V takovém případě můžete chtít říct indexeru ignorovat chyby. To provést nastavením *maxFailedItems* a *maxFailedItemsPerBatch* jako -1 jako součást definice indexeru.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tip 4: Pohled na obohacené dokumenty pod kapotou 
Obohacené dokumenty jsou dočasné struktury vytvořené během obohacení a potom odstraněny po dokončení zpracování.

Pokud chcete zachytit snímek rozšířeného dokumentu vytvořeného během indexování, přidejte do indexu pole s názvem ```enriched```. Indexer do tohoto pole automaticky vypíše řetězcovou reprezentaci všech rozšíření daného dokumentu.

Pole ```enriched``` bude obsahovat řetězec, který je logickou reprezentací rozšířeného dokumentu uloženého v paměti ve formátu JSON.  Hodnota pole je ale platný dokument JSON. Abyste si mohli dokument zobrazit jako formátovaný dokument JSON, bude nutné nahradit `\"` za `"`, protože pro uvozovky se používají řídicí sekvence. 

Rozšířené pole je určeno pouze pro účely ladění, které vám pomohou pochopit logický tvar obsahu, proti kterému jsou výrazy vyhodnocovány. Pro účely indexování byste neměli být závislí na tomto poli.

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Tip 5: Očekávaný obsah se nezobrazí

Chybějící obsah může být výsledkem získávání dokumentů během indexování. Úrovně Free a Basic mají nízké limity pro velikost dokumentu. Během indexování je vynechán jakýkoli soubor překračující limit. Vynechaných dokumentů můžete zkontrolovat na webu Azure Portal. Na řídicím panelu vyhledávací služby poklikejte na dlaždici Indexery. Zkontrolujte poměr úspěšných indexovaných dokumentů. Pokud to není 100 %, můžete kliknout na poměr získat více podrobností. 

Pokud problém souvisí s velikostí souboru, může se zobrazit \<chyba, jako je tato: \<"Název souboru objektů blob>" má velikost> bajtů velikosti souboru, která přesahuje maximální velikost extrakce dokumentu pro aktuální úroveň služby." Další informace o limitech indexeru naleznete v tématu [Service limits](search-limits-quotas-capacity.md).

Druhým důvodem, proč se obsah nezobrazí, mohou být související chyby mapování vstupů a výstupů. Například název cíle výstupu je "Lidé", ale název pole indexu je malá písmena "lidé". Systém může vrátit 201 zprávy o úspěchu pro celý kanál, takže si myslíte, že indexování proběhlo úspěšně, i když ve skutečnosti je pole prázdné. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 6: Rozšíření zpracování nad maximální dobu provozu (24hodinové okno)

Analýza obrazu je výpočtově náročná i pro jednoduché případy, takže pokud jsou obrázky obzvláště velké nebo složité, mohou doba zpracování překročit maximální povolenou dobu. 

Maximální doba běhu se liší podle úrovně: několik minut na úrovni Free, 24hodinové indexování na fakturovatelných úrovních. Pokud se zpracování nepodaří dokončit během 24 hodin pro zpracování na vyžádání, přepněte na plán, aby indexer vyzvednout zpracování tam, kde to skončilo. 

U naplánovaných indexerů indexování pokračuje podle plánu na poslední známé dobré dokumentu. Pomocí opakovaného plánu, indexer může pracovat svou cestu přes nevyřízené položky obrázku přes řadu hodin nebo dnů, dokud všechny nezpracované obrázky jsou zpracovány. Další informace o syntaxi plánu najdete [v tématu Krok 3: Vytvoření indexeru](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) nebo [jak naplánovat indexery pro Azure Cognitive Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Pokud je indexer nastaven na určitý plán, ale opakovaně selže ve stejném dokumentu znovu a znovu při každém spuštění, indexer začne běžet v méně častém intervalu (až do maximálního počtu alespoň jednou za 24 hodin), dokud úspěšně neprovede pokrok Znovu.  Pokud se domníváte, že jste opravili jakýkoli problém, který způsoboval, že indexer byl v určitém okamžiku zablokovaný, můžete provést spuštění indexeru na vyžádání, a pokud to úspěšně provede pokrok, indexer se znovu vrátí do nastaveného intervalu plánu.

Pro indexování založené na portálu (jak je popsáno v rychlém startu), výběr možnosti indexeru "spustit jednou" omezuje zpracování na 1 hodinu (`"maxRunTime": "PT1H"`). Možná budete chtít rozšířit okno zpracování na něco delší.

## <a name="tip-7-increase-indexing-throughput"></a>Tip 7: Zvýšení propustnosti indexování

Pro [paralelní indexování](search-howto-large-index.md)umístěte data do více kontejnerů nebo více virtuálních složek uvnitř stejného kontejneru. Pak vytvořte více párů zdrojů dat a indexeru. Všechny indexery můžete použít stejné skillset a zapisovat do stejného indexu hledání cíle, takže vaše vyhledávací aplikace nemusí být vědomi tohoto dělení.
Další informace naleznete v [tématu Indexování velkých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Viz také
+ [Úvodní příručka: Vytvoření kanálu obohacení umělou a imtou na portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Naučte se api REST REST obohacení ai](cognitive-search-tutorial-blob.md)
+ [Určení pověření zdroje dat](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexování velkých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Jak mapovat obohacená pole do indexu](cognitive-search-output-field-mapping.md)
