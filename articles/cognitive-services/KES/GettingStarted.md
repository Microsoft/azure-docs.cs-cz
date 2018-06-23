---
title: Začínáme se službou zkoumání znalostní báze | Microsoft Docs
description: Znalostní báze zkoumání služby (KES) použijte k vytvoření modulu pro interaktivní vyhledávání prostředí napříč academic publikace v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 02dc9368eef02d6fa507335ef3171e923412acca
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342741"
---
<a name="getting-started"></a>
# <a name="get-started-with-the-knowledge-exploration-service"></a>Začínáme se službou zkoumání znalostní báze
V tomto návodu použijete k vytvoření modul pro hledání interaktivní prostředí pro publikace, academic Knowledge zkoumání služby (KES). Můžete nainstalovat nástroj příkazového řádku [ `kes.exe` ](CommandLine.md)a všechny soubory příklad z [sady SDK služby zkoumání znalostní báze](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Příklad academic publikace obsahuje ukázku 1000 academic dokumenty Paper publikovaná odborníky společnosti Microsoft.  Každý dokument je přidružen název, publikace rok, autoři a klíčová slova. Každý Autor je reprezentována ID, názvu a přidružení v době publikace. Každý – klíčové slovo lze přidružit sadu synonyma (například – klíčové slovo "support vector machine" může být přidružen synonymum "svm").

<a name="defining-schema"></a>
## <a name="define-the-schema"></a>Definování schématu
Schéma popisuje strukturu atribut objektů v doméně. Určuje název a datový typ pro každý atribut ve formátu souboru JSON. Následující příklad je obsah souboru *Academic.schema*.

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

Tady můžete definovat *název*, *roku*, a *– klíčové slovo* jako řetězec, celé číslo a řetězec atributu, v uvedeném pořadí. Protože autoři jsou reprezentované pomocí ID, názvu a přidružení, definujete *Autor* jako složený atribut s tři dílčí atributy: *Author.Id*, *Author.Name*, a *Author.Affiliation*.

Ve výchozím nastavení, atributy podporují všechny operace, které jsou k dispozici pro datový typ, včetně *rovná*, *starts_with*, a *is_between*. Vzhledem k tomu, že autor ID se používá pouze interně jako identifikátor, přepsat výchozí nastavení a zadejte *rovná* jako jediný indexované operaci.

Pro *– klíčové slovo* atribut, povolí synonyma k hodnotám kanonický – klíčové slovo zadáním soubor synonymum *Keyword.syn* v definici atributu. Tento soubor obsahuje seznam kanonický synonymum párů a hodnot:

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

Další informace o definici schématu, najdete v části [formát schématu](SchemaFormat.md).

<a name="generating-data"></a>
## <a name="generate-data"></a>Generování dat
Datový soubor popisuje seznam publikací pro každý řádek zadání hodnot atributů dokumentu v indexu [formátu JSON](http://json.org/).  Následující příklad je jeden řádek z datového souboru *Academic.data*naformátovanou čitelnější:

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

V tento fragment kódu, můžete zadat *název* a *roku* atribut dokumentu jako řetězce formátu JSON a číslo, v uvedeném pořadí. Více hodnot, jsou reprezentované pomocí pole JSON. Protože *Autor* je atribut složené každá hodnota je reprezentována pomocí objekt JSON, který se skládá z jeho dílčí atributy. Atributy s chybějící hodnoty, jako například *– klíčové slovo* v tomto případě mohou být vyloučeny z reprezentace JSON.

K odlišení pravděpodobnost různé dokumenty Paper, zadejte relativní protokolu pravděpodobnost pomocí integrované *logprob* atribut. Zadané pravděpodobnost *p* mezi 0 a 1, výpočetní pravděpodobnost protokolu jako protokolu (*p*), kde log() je funkce přirozené protokolu.

Další informace najdete v tématu [formát dat](DataFormat.md).

<a name="building-index"></a>
## <a name="build-a-compressed-binary-index"></a>Sestavení komprimované binární indexu
Až budete mít soubor dat a souboru schématu, můžete vytvořit index na komprimované binární datové objekty pomocí [ `kes.exe build_index` ](CommandLine.md#build_index-command). V tomto příkladu vytvoříte soubor indexu *Academic.index* ze souboru vstupní schéma *Academic.schema* a datový soubor *Academic.data*. Použijte následující příkaz:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Pro rychlé při vytváření prototypu mimo Azure [ `kes.exe build_index` ](CommandLine.md#build_index-command) malé indexy místně, můžete vytvořit z datové soubory obsahující až 10 000 objektů. Pro větší datové soubory, můžete buď spustit příkaz uvnitř [virtuální počítač s Windows v Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), nebo provádět Vzdálená sestavení v Azure. Podrobnosti najdete v tématu [vertikálním navýšení kapacity](#scaling-up).

<a name="authoring-grammar"></a>
## <a name="use-an-xml-grammar-specification"></a>Pomocí specifikace gramatika XML
Gramatiky určuje sadu přirozeného jazyka dotazů, které služba dokáže interpretovat, a také jak jsou tyto dotazy v přirozeném jazyce přeložit na výrazy sémantický dotaz. V tomto příkladu použijete gramatika zadaný v *academic.xml*:

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

Další informace o syntaxi specifikace gramatika najdete v tématu [gramatika formátu](GrammarFormat.md).

<a name="compiling-grammar"></a>
## <a name="compile-the-grammar"></a>Kompilace gramatiky
Až budete mít specifikace gramatika XML, můžete ji můžete zkompilovat do binární gramatika pomocí [ `kes.exe build_grammar` ](CommandLine.md#build_grammar-command). Všimněte si, že pokud gramatiky importuje schéma, soubor schématu musí být umístěné ve stejnou cestu jako gramatika XML. V tomto příkladu vytvoříte soubor binární gramatika *Academic.grammar* ze vstupního souboru XML gramatika *Academic.xml*. Použijte následující příkaz:

`kes.exe build_grammar Academic.xml Academic.grammar`

<a name="hosting-index"></a>
## <a name="host-the-grammar-and-index-in-a-web-service"></a>Hostovat gramatika a index ve webové službě
Pro rychlé vytváření prototypů, je možné hostovat gramatika a index ve webové službě v místním počítači, pomocí [ `kes.exe host_service` ](CommandLine.md#host_service-command). Potom přístup ke službě přes [webovým rozhraním API](WebAPI.md) ověření dat správnost a gramatické návrhu. V tomto příkladu je soubor gramatika hostovat *Academic.grammar* a soubor indexu *Academic.index* v http://localhost:8000/. Použijte následující příkaz:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Tím se vyvolá místní instance webové služby. Můžete interaktivně testovat službu navštivte stránky `http::localhost:<port>` z prohlížeče. Další informace najdete v tématu [testování služby](#testing-service).

Můžete také přímo volat různé [webovým rozhraním API](WebAPI.md) k testování přirozené Interpretace jazyka, dokončení dotazu, strukturovaných dotazů zkušební a výpočetní histogram. K zastavení služby, zadejte "ukončení" do `kes.exe host_service` příkazový řádek nebo stiskněte kombinaci kláves Ctrl + C. Zde je několik příkladů:

* [http://localhost:8000/interpret?query=papers podle susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers podle susan t d & dokončení = 1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais') & attributes=Title,Year,Author.Name,Author.Id & count = 2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'), rok > = 2013) & atributy = rok, – klíčové slovo a počet = 4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Mimo Azure [ `kes.exe host_service` ](CommandLine.md#host_service-command) je omezený na indexů až 10 000 objektů. Další omezení zahrnují počet požadavků za sekundu, 10 rozhraní API a celkový počet požadavků na 1000 před proces automaticky ukončí. K porušení těchto omezení, spusťte příkaz z uvnitř [virtuální počítač s Windows v Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), nebo nasazení do cloudové služby Azure pomocí [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) příkaz. Podrobnosti najdete v tématu [nasazení služby](#deploying-service).

<a name="scaling-up"></a>
## <a name="scale-up-to-host-larger-indices"></a>Škálování na větší indexy, které jsou hostiteli
Pokud používáte `kes.exe` index mimo Azure, je omezený na 10 000 objektů. Můžete sestavit a hostitelů větší indexy pomocí Azure. Zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/). Případně, pokud se přihlásíte k sadě Visual Studio nebo MSDN, můžete [aktivovat výhody předplatitele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Tyto nabízejí některé kredity Azure každý měsíc.

Chcete-li povolit `kes.exe` přístup k účtu Azure [stáhněte soubor nastavení publikování Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) z portálu Azure. Pokud budete vyzváni, přihlaste se k požadovaný účet Azure. Uložte soubor jako *AzurePublishSettings.xml* v pracovním adresáři odkud `kes.exe` běží.

Existují dva způsoby, jak vytvářet a hostovat velký indexy. První je Příprava schématu a datových souborů v systému Windows virtuálního počítače v Azure. Spusťte [ `kes.exe build_index` ](#building-index) k sestavení indexu místně na virtuálním počítači bez jakýchkoli omezení velikosti. Výsledný index může být hostitelem místně virtuálního počítače pomocí [ `kes.exe host_service` ](#hosting-service) pro rychlé vytváření prototypů znovu bez jakýchkoli omezení. Podrobné pokyny najdete v tématu [virtuálního počítače Azure kurzu](../../../articles/virtual-machines/windows/quick-create-portal.md).

Druhý způsob spočívá v provést vzdálené Azure sestavení, pomocí [ `kes.exe build_index` ](CommandLine.md#build_index-command) s `--remote` parametr. Toto nastavení určuje velikost virtuálního počítače Azure. Když `--remote` je zadán parametr, příkaz vytvoří dočasný virtuální počítač Azure této velikosti. Potom index je založený na virtuálním počítači, odešle do úložiště objektů blob cílový index a odstraní virtuální počítač po dokončení. Vaše předplatné Azure je účtován nákladů na virtuální počítač při sestavuje index.

Díky této možnosti vzdáleného Azure sestavení [ `kes.exe build_index` ](CommandLine.md#build_index-command) ke spuštění v jakémkoli prostředí. Při provádění vzdálené sestavení, vstupní argumenty schéma a data, může být místní cesty souborů nebo [úložiště objektů blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) adresy URL. Argument indexu výstup musí být adresu URL úložiště objektů blob. Pokud chcete vytvořit účet úložiště Azure, najdete v části [účty Azure storage](../../storage/common/storage-create-storage-account.md). Ke zkopírování souborů efektivně do a z úložiště objektů blob, použijte [AzCopy](../../storage/common/storage-use-azcopy.md) nástroj.

V tomto příkladu můžete předpokládat, že již byla vytvořena následující kontejner úložiště objektů blob: http://&lt;*účet*&gt;.blob.core.windows.net/&lt;*kontejneru* &gt;/. Obsahuje schéma *Academic.schema*, soubor odkazované synonymum *Keywords.syn*a po kompletní datový soubor *Academic.full.data*. Můžete vytvořit index úplné vzdáleně pomocí následujícího příkazu:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Všimněte si, že může trvat 5 až 10 minut ke zřízení temporay virtuálních počítačů k sestavení indexu. Pro rychlé vytváření prototypů můžete:
- Vývoj s menší datové sady místně na libovolném počítači.
- Ručně [vytvořit virtuální počítač Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), [k nim připojit](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) přes vzdálenou plochu, nainstalujte [sady SDK služby zkoumání znalostní báze](https://www.microsoft.com/en-us/download/details.aspx?id=51488)a spusťte [ `kes.exe` ](CommandLine.md) z virtuálního počítače.

Stránkování zpomaluje procesu sestavení. Abyste se vyhnuli stránkování, používejte virtuální počítač s odpovídající trojnásobku velikosti paměti RAM jako velikost souboru vstupní data pro vytvoření indexu. Použijte virtuální počítač s 1 GB více paměti RAM než velikost indexu pro hostování. Seznam dostupných velikostí virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

<a name="deploying-service"></a>
## <a name="deploy-the-service"></a>Nasazení služby
Až budete mít gramatika a indexu, jste připraveni k nasazení služby do cloudové služby Azure. Pokud chcete vytvořit novou službu cloudu Azure, najdete v části [postup vytvoření a nasazení cloudové služby](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). V tomto okamžiku nezadávejte balíčku pro nasazení.  

Pokud jste vytvořili cloudové službě, můžete použít [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) k nasazení služby. Cloudové služby Azure má dva nasazovací sloty: provozní prostředí a Fázování. Pro službu, která přijímá provoz uživatele za provozu měli byste původně nasadit pro přípravný slot. Čekání na spuštění a inicializaci sám sebe služby. Potom můžete odeslat několik požadavků na ověření nasazení a ověřte, že předává základní testy.

[Swap –](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) obsah pracovní pozici s produkční slot tak, aby za provozu je nyní přenášená na nově nasazené služby. Při nasazování aktualizovanou verzi služby s nová data, můžete tento proces opakovat. Jako u všech jiných služeb cloudu Azure, můžete volitelně použít portál Azure ke konfiguraci [automatické škálování](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

V tomto příkladu je nasadit *Academic* index pro přípravný slot stávající cloudovou službu s *< vm_size >* virtuálních počítačů. Použijte následující příkaz:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Seznam dostupných velikostí virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Poté, co nasadíte službu, můžete volat různé [webovým rozhraním API](WebAPI.md) k testování přirozené Interpretace jazyka, dokončení dotazu, strukturovaných dotazů zkušební a výpočetní histogram.  

<a name="testing-service"></a>
## <a name="test-the-service"></a>Testování služby
Ladění za provozu služby, přejděte na hostitelském počítači z webového prohlížeče. Pro místní službu nasadit prostřednictvím [host_service](#hosting-service), navštivte `http://localhost:<port>/`.  Azure cloud služby nasazené prostřednictvím [deploy_service](#deploying-service), navštivte `http://<serviceName>.cloudapp.net/`.

Tato stránka obsahuje odkaz na informace o základní statistické údaje volání rozhraní API, jakož i gramatika a index hostovanou na tuto službu. Tato stránka také obsahuje rozhraní interaktivní vyhledávání, která demonstruje použití webových rozhraní API. Zadejte do vyhledávacího pole zobrazíte výsledky dotazů [interpretovat](interpretMethod.md), [vyhodnotit](evaluateMethod.md), a [calchistogram](calchistogramMethod.md) volání rozhraní API. Podkladovém zdroji HTML části této stránky slouží taky jako příklad toho, jak integrovat webové rozhraní API do aplikace, k vytvoření prostředí bohatý a interaktivní vyhledávání.


