---
title: 'Příklad: Získávání spuštění – Knowledge Exploration Service API'
titlesuffix: Azure Cognitive Services
description: Použijte rozhraní API služby KES (Knowledge Exploration Service) k vytvoření modulu pro interaktivní hledání v akademických publikacích.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: e2bb5550cfe07064d595151305955d87f9c61050
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819531"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Začínáme se službou Knowledge Exploration Service

V tomto článku s návodem použijete službu KES (Knowledge Exploration Service) k vytvoření modulu pro interaktivní hledání pro akademické publikace. Můžete si nainstalovat nástroj příkazového řádku [`kes.exe`](CommandLine.md) a všechny ukázkové soubory ze [sady SDK služby Knowledge Exploration Service](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Příklad akademických publikací obsahuje ukázku 1 000 akademických dokumentů publikovaných výzkumníky u Microsoftu.  Každý dokument je spojený s názvem, rokem publikování, autory a klíčovými slovy. Každý autor je reprezentován ID, jménem a afilací v době publikování. Každé klíčové slovo může být spojené se sadou synonym (například klíčové slovo „support vector machine“ může být spojené se synonymem „svm“).

## <a name="define-the-schema"></a>Definování schématu

Schéma popisuje strukturu atributů objektů v doméně. Určuje název a datový typ každého atributu ve formátu souboru JSON. Následující příklad je obsah souboru *Academic.schema*.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Definujete tady atributy *Title* (Název), *Year* (Rok) a *Keyword* (Klíčové slovo) jako atributy typů řetězec (String), celé číslo (Int32) a řetězec (String). Protože autory jsou reprezentovány ID, název a umístění, můžete definovat *Autor* jako složený atribut s tři dílčí atributy: *Author.Id*, *Author.Name*, a *Author.Affiliation*.

Atributy ve výchozím nastavení podporují všechny operace dostupné pro jejich datový typ, včetně operací *equals*, *starts_with* a *is_between*. Protože se atribut Author.ID používá jako identifikátor jenom interně, přepište výchozí nastavení a jako jedinou indexovanou operaci zadejte *equals*.

Pro atribut *Keyword* povolte porovnávání synonym s kanonickými hodnotami klíčových slov – zadáním souboru synonym *Keyword.syn* v definici atributu. Tento soubor obsahuje seznam dvojic kanonická hodnota – hodnota synonyma:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Další informace o definování schématu najdete v článku [Formát schématu](SchemaFormat.md).

## <a name="generate-data"></a>Generování dat

Datový soubor popisuje seznam publikací k indexování – každý řádek určuje hodnoty atributů dokumentu ve [formátu JSON](http://json.org/).  Následující příklad je jeden řádek ze souboru *Academic.data*, který je formátovaný pro zlepšení čitelnosti:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

V tomto fragmentu zadáte atributy *Title* (Název) a *Year* (Rok) dokumentu jako JSON řetězec a číslo (v tomto pořadí). Vícehodnotové atributy jsou reprezentovány pomocí polí JSON. Protože atribut *Author* (Autor) je složený atribut, je každá hodnota reprezentována pomocí objektu JSON skládajícího se z dílčích atributů. Atributy s chybějícími hodnotami, jako je v tomto případě atribut *Keyword* (Klíčové slovo) můžou být v reprezentaci JSON vynechány.

K rozlišení pravděpodobnosti různých dokumentů zadejte relativní logaritmickou pravděpodobnost pomocí předdefinovaného atributu *logprob*. Za předpokladu, že pravděpodobnost *p* je mezi 0 a 1, spočítáte logaritmickou pravděpodobnost jako log(*p*), kde log() ke funkce přirozeného logaritmu.

Další informace najdete v článku [Formát dat](DataFormat.md).

## <a name="build-a-compressed-binary-index"></a>Sestavení komprimovaného binárního indexu

Až budete mít soubor schématu a datový soubor, můžete sestavit komprimovaný binární index datových objektů pomocí příkazu [`kes.exe build_index`](CommandLine.md#build_index-command). V tomto příkladu sestavíte soubor indexu *Academic.index* ze vstupního souboru schématu *Academic.schema* a datového souboru *Academic.data*. Použijte následující příkaz:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Při rychlém vytváření prototypů mimo Azure můžete příkazem [`kes.exe build_index`](CommandLine.md#build_index-command) sestavit malé indexy místně – z datových souborů obsahujících maximálně 10 000 objektů. Pro větší datové soubory můžete příkaz spustit v rámci [virtuálního počítače s Windows v Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) nebo provést vzdálené sestavení indexu v Azure. Podrobnosti najdete v tématu škálování nahoru.

## <a name="use-an-xml-grammar-specification"></a>Použití XML specifikace gramatiky

Gramatika určuje sadu dotazů v přirozeném jazyce, které dokáže služba interpretovat, a také to, jak se tyto dotazy v přirozeném jazyce překládají do sémantických výrazů dotazu. V tomto příkladu použijete gramatiku určenou v souboru *academic.xml*:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Další informace o syntaxi specifikace gramatiky najdete v článku [Formát gramatiky](GrammarFormat.md).

## <a name="compile-the-grammar"></a>Kompilace gramatiky

Až budete mít XML specifikaci gramatiky, můžete ji zkompilovat do binární gramatiky pomocí příkazu [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Pamatujte na to, že pokud gramatika importuje schéma, musí být soubor schématu umístěný ve stejné cestě jako XML specifikace gramatiky. V tomto příkladu sestavíte binární soubor gramatiky *Academic.grammar* ze vstupního XML souboru gramatiky *Academic.xml*. Použijte následující příkaz:

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Hostování gramatiky a indexu ve webové službě

Při rychlém vytváření prototypů můžete gramatiku a index hostovat ve webové službě na místním počítači pomocí příkazu [`kes.exe host_service`](CommandLine.md#host_service-command). Ke službě se pak můžete dostat prostřednictvím [webových rozhraní API](WebAPI.md) a ověřit správnost dat a návrh gramatiky. V tomto příkladu budete hostovat soubor gramatiky *Academic.grammar* a soubor indexu *Academic.index* na http://localhost:8000/. Použijte následující příkaz:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Tím se iniciuje místní instance webové služby. Když navštívíte `http::localhost:<port>` z prohlížeče, můžete službu interaktivně testovat. Další informace najdete v tématu otestování služby.

Můžete také přímo volat různá [webová rozhraní API](WebAPI.md) a testovat interpretaci přirozeného jazyka, kompletaci dotazu, vyhodnocení strukturovaného dotazu a výpočet histogramu. Pokud chcete službu zastavit, zadejte na příkazovém řádku `kes.exe host_service` „quit“ nebo stiskněte Ctrl+C. Zde je několik příkladů:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Mimo Azure je příkaz [`kes.exe host_service`](CommandLine.md#host_service-command) omezený na indexy s maximálně 10 000 objekty. K dalším omezením patří rychlost rozhraní API maximálně 10 žádostí za sekundu a automatické ukončení procesu po celkovém počtu 1 000 žádostí. Pokud chcete tato omezení obejít, spusťte příkaz v rámci [virtuálního počítače s Windows v Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) nebo službu nasaďte do cloudové služby Azure pomocí příkazu [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Podrobnosti najdete v tématu nasazení služby.

## <a name="scale-up-to-host-larger-indices"></a>Vertikální navýšení kapacity pro hostování větších indexů

Když `kes.exe` spustíte mimo Azure, je index omezený na 10 000 objektů. Při použití Azure můžete sestavit a hostovat větší indexy. Zaregistrujte se k [bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/). Pokud se případně přihlásíte k odběru Visual Studia nebo MSDN, můžete si [aktivovat výhody pro předplatitele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ty nabízejí každý měsíc nějaké kredity Azure.

Abyste nástroji `kes.exe` umožnili přístup k účtu Azure, [stáhněte si soubor Azure Publish Settings](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) z portálu Azure Portal. Pokud se zobrazí výzva, přihlaste se k požadovanému účtu Azure. Soubor uložte jako *AzurePublishSettings.xml* do pracovního adresáře, ze kterého spouštíte `kes.exe`.

Existují dva způsoby, jak sestavit a hostovat velké indexy. První metodou je připravit schéma a datové soubory ve virtuálním počítači s Windows v Azure. Potom se spuštěním příkazu [`kes.exe build_index`](#building-index) sestaví index místně na virtuálním počítači, bez jakýchkoli omezení velikosti. Výsledný index se může při rychlém vytváření prototypů hostovat místně na virtuálním počítači pomocí příkazu [`kes.exe host_service`](#hosting-service), zase bez jakýchkoli omezení. Podrobný postup najdete v [kurzu virtuálního počítače Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

Druhou metodou je provést vzdálené sestavení v Azure pomocí příkazu [`kes.exe build_index`](CommandLine.md#build_index-command) s parametrem `--remote`. Ten určuje velikost virtuálního počítače Azure. Když je zadaný parametr `--remote`, příkaz vytvoří dočasný virtuální počítač Azure této velikosti. Pak na virtuálním počítači sestaví index, nahraje index do cílového úložiště objektů blob a při dokončení odstraní virtuální počítač. U vašeho předplatného Azure budou účtovány náklady na virtuální počítač během sestavování indexu.

Tato schopnost vzdáleného sestavení v Azure umožňuje spustit příkaz [`kes.exe build_index`](CommandLine.md#build_index-command) v libovolném prostředí. Když provádíte vzdálené sestavení, argumenty pro vstupní schéma a data můžou být místní cesty souborů nebo adresy URL [úložiště Azure objektů blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Argument pro výstupní index musí být adresa URL úložiště objektů blob. Informace o vytvoření účtu úložiště Azure najdete v článku [o účtech úložiště Azure](../../storage/common/storage-create-storage-account.md). K efektivnímu kopírování souborů do úložiště objektů blob a opačně použijte nástroj [AzCopy](../../storage/common/storage-use-azcopy.md).

V tomto příkladu můžete předpokládat, že už je vytvořený následující kontejner úložiště objektů blob: http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. Obsahuje schéma *Academic.schema*, odkazovaný soubor synonym *Keywords.syn* a kompletní datový soubor *Academic.full.data*. Úplný index můžete vzdáleně sestavit pomocí následujícího příkazu:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Pamatujte na to, že zřízení dočasného virtuálního počítače k sestavení indexu může trvat 5 až 10 minut. Při rychlém vytváření prototypů můžete:
- Vyvíjet s menší datovou sadou místně na libovolném počítači.
- Ručně [vytvořit virtuální počítač Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), [připojit se k němu](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) přes Vzdálenou plochu, nainstalovat [sadu SDK služby Knowledge Exploration Service](https://www.microsoft.com/en-us/download/details.aspx?id=51488) a spustit [`kes.exe`](CommandLine.md) v rámci virtuálního počítače.

Stránkování zpomaluje proces sestavení. Abyste stránkování zabránili, použijte k sestavení indexu virtuální počítač s pamětí RAM třikrát větší, než je velikost vstupního datového souboru. Použijte virtuální počítač s pamětí RAM o 1 GB větší, než je velikost indexu, který chcete hostovat. Seznam dostupných velikostí virtuálních počítačů najdete v článku [Velikosti virtuálních počítačů](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>Nasazení služby

Až budete mít gramatiku a index, můžete službu nasadit do cloudové služby Azure. Informace o vytvoření nové cloudové služby Azure najdete v článku [Jak vytvořit a nasadit cloudovou službu](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Teď nezadávejte balíček pro nasazení.  

Po vytvoření cloudové služby můžete pomocí příkazu [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) službu nasadit. Cloudová služba Azure má dva sloty nasazení: přípravný a produkční. Službu, která bude přijímat živý uživatelský provoz, byste měli nejdříve nasadit do přípravného slotu. Počkejte, až se služba spustí a inicializuje. Pak můžete poslat několik požadavků, abyste ověřili nasazení a zkontrolovali, jestli úspěšně projde základními testy.

[Prohoďte](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) obsahy přípravného slotu a produkčního slotu, aby byl živý provoz odteď směrovaný na nově nasazenou službu. Když nasadíte aktualizovanou verzi služby s novými daty, můžete tento proces zopakovat. Stejně jako u všech dalších cloudových služeb Azure můžete pomocí portálu Azure Portal nakonfigurovat [automatické škálování](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

V tomto příkladu nasadíte index *Academic* do přípravného slotu existující cloudové služby s virtuálními počítači o velikosti *<vm_size>*. Použijte následující příkaz:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Seznam dostupných velikostí virtuálních počítačů najdete v článku [Velikosti virtuálních počítačů](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Po nasazení služby můžete volat různá [webová rozhraní API](WebAPI.md) a testovat interpretaci přirozeného jazyka, kompletaci dotazu, vyhodnocení strukturovaného dotazu a výpočet histogramu.  

## <a name="test-the-service"></a>Testování služby

Když chcete ladit živou službu, přejděte ve webovém prohlížeči na hostitelský počítač. Pro místní služby nasazené prostřednictvím host_service, navštivte `http://localhost:<port>/`.  Pro cloudové služby Azure nasazené prostřednictvím deploy_service, navštivte `http://<serviceName>.cloudapp.net/`.

Tato stránka obsahuje odkaz na informace o základní statistice volání rozhraní API a také gramatiku a index, které jsou hostované u této služby. Tato stránka obsahuje také rozhraní pro interaktivní hledání, které ukazuje použití webových rozhraní API. Do vyhledávacího pole zadejte dotazy, aby se zobrazily výsledky volání metod rozhraní API [interpret](interpretMethod.md), [evaluate](evaluateMethod.md) a [calchistogram](calchistogramMethod.md). Podkladový zdroj HTML této stránky také slouží jako příklad, jak webová rozhraní API integrovat do aplikace a vytvořit bohaté prostředí pro interaktivní hledání.


