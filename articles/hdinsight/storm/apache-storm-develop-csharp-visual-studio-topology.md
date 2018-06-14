---
title: Topologií Apache Storm sadou Visual Studio a C# – Azure HDInsight | Microsoft Docs
description: Zjistěte, jak k vytváření topologie Storm v jazyce C#. Vytvořte topologii počtu jednoduché aplikace word v sadě Visual Studio pomocí nástroje Hadoop pro sadu Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: ''
ms.devlang: java
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 1e35d795fb65c837f7a4152920f701da5bf8f506
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421426"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Vývoj topologie C# pro Apache Storm pomocí nástrojů Data Lake pro Visual Studio

Naučte se vytvářet topologie C# Storm pomocí nástrojů Azure Data Lake (Hadoop) pro Visual Studio. Tento dokument vás provede procesem vytvoření projektu Storm v sadě Visual Studio, místní testování a nasazení do Apache Storm v clusteru Azure HDInsight.

Můžete také Naučte se vytvářet hybridní topologie, které používají C# a komponent v jazyce Java.

> [!NOTE]
> Pokud kroky v tomto dokumentu se spoléhají na vývojového prostředí systému Windows pomocí sady Visual Studio, zkompilovaný projekt lze odeslat do clusteru Linux nebo HDInsight se systémem Windows. Clustery se systémem Linux vytvořené po 28 říjnu 2016 se podporují jenom SCP.NET topologie.

Pokud chcete používat topologie C# s clusterem se systémem Linux, je třeba aktualizovat balíček Microsoft.SCP.Net.SDK NuGet používá váš projekt na verzi 0.10.0.6 nebo novější. Verze balíčku se zároveň musí shodovat s hlavní verzí Stormu nainstalovanou ve službě HDInsight.

| HDInsight verze | Storm verze | SCP.NET verze | Výchozí Mono verze |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(jenom na HDInsight se systémem Windows) | Není k dispozici |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> Topologie jazyka C# v clusterech založených na Linuxu musí používat technologii .NET 4.5. a pro spuštění v clusteru HDInsight musí používat Mono. Zkontrolujte [Mono kompatibility](http://www.mono-project.com/docs/about-mono/compatibility/) pro potenciální nekompatibility.

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

Pomocí jedné z následujících verzí sady Visual Studio můžete vyvíjet topologie C# s SCP.NET:

* Visual Studio 2012 s [aktualizací 4](http://www.microsoft.com/download/details.aspx?id=39305)

* Visual Studio 2013 s [aktualizací 4](http://www.microsoft.com/download/details.aspx?id=44921) nebo [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 nebo [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (všechny edice)

## <a name="install-data-lake-tools-for-visual-studio"></a>Nástroje pro instalaci Data Lake pro Visual Studio

Instalace nástrojů Data Lake pro Visual Studio, postupujte podle kroků v [Začínáme pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Instalace Javy

Při odesílání topologie Storm ze sady Visual Studio SCP.NET generuje soubor zip, který obsahuje topologie a závislosti. Java se používá k vytvoření tyto soubory zip, protože používá formátu, který je více kompatibilní s clustery se systémem Linux.

1. Nainstalujte Java Developer Kit (JDK) 7 nebo novější na vašem vývojovém prostředí. Můžete získat JDK Oracle z [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Můžete také použít [jiných distribuce Java](http://openjdk.java.net/).

2. `JAVA_HOME` Proměnnou prostředí musí odkazovat na adresář, který obsahuje Java.

3. `PATH` Musí obsahovat proměnné prostředí `%JAVA_HOME%\bin` adresáře.

Chcete-li ověřit, zda jsou správně nainstalován Java a sadu JDK, můžete použít následující konzolovou aplikaci C#:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable(“JAVA_HOME”);
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @”\bin”, “jar.exe”);
               if (File.Exists(jarExe))
               {
                   Console.WriteLine(“JAVA Is Installed properly”);
                    return;
               }
               else
               {
                   Console.WriteLine(“A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.”);
               }
           }
           else
           {
             Console.WriteLine(“A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.”);
           }
       }  
   }
}
```

## <a name="storm-templates"></a>Šablony Storm

Nástroje Data Lake pro Visual Studio poskytují následujících šablon:

| Typ projektu | Demonstruje |
| --- | --- |
| Aplikace Storm |Prázdný projekt topologie Storm. |
| Ukázka zapisovače Azure SQL Storm |Jak napsat do Azure SQL Database. |
| Ukázka čtečky Azure Cosmos DB Storm |Jak číst z Azure Cosmos DB. |
| Ukázka zapisovače Azure Cosmos DB Storm |Postup zápisu do databáze Azure Cosmos. |
| Ukázka EventHub čtečky Storm |Jak číst z Azure Event Hubs. |
| Ukázka EventHub zapisovače Storm |Jak zapsat do služby Azure Event Hubs. |
| Ukázka HBase čtečky Storm |Jak číst z HBase v HDInsight clustery. |
| Ukázka HBase zapisovače Storm |Postup zápisu HBase v HDInsight clustery. |
| Ukázka hybridní Storm |Postup používání komponent prostředí Java. |
| Ukázka Storm |Počet topologii základní aplikace word. |

> [!WARNING]
> Ne všechny šablony pracovat s HDInsight se systémem Linux. Balíčky NuGet, které jsou používané šablony nemusí být kompatibilní s Mono. Zkontrolujte [Mono kompatibility](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentu a použít [.NET přenositelnost analyzátor](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) zjistit potenciální problémy.

Kroky v tomto dokumentu použijete k vytvoření topologii základní aplikace Storm typ projektu.

### <a name="hbase-templates-notes"></a>Poznámky k šablony HBase

Šablony čtení a zápis HBase REST API HBase, není rozhraní API HBase Java, používají ke komunikaci s HBase v clusteru HDInsight.

### <a name="eventhub-templates-notes"></a>Poznámky k EventHub šablony

> [!IMPORTANT]
> Komponentu spout založené na jazyce Java EventHub součástí šablony EventHub čtečky nemusí pracovat se Storm v HDInsight verze 3.5 nebo novější. Aktualizovanou verzi této součásti je k dispozici na [Githubu](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Ukázkové topologie, který používá tato součást a spolupracuje s Storm v HDInsight 3.5, najdete v části [Githubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Vytvoření topologie C#

1. Otevřete Visual Studio, vyberte **soubor** > **nový**a potom vyberte **projektu**.

2. Z **nový projekt** okně rozbalte **nainstalovaná** > **šablony**a vyberte **Azure Data Lake**. V seznamu šablon vyberte **aplikace Storm**. V dolní části obrazovky, zadejte **WordCount** jako název aplikace.

    ![Snímek obrazovky nový projekt – okno](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Po vytvoření projektu, musí mít následující soubory:

   * **Program.cs**: Tento soubor definuje topologie pro váš projekt. Ve výchozím nastavení se vytvoří výchozí topologie, která se skládá z jedné spout a jeden bolt.

   * **Spout.cs**: Příklad funkcí spout, který vysílá náhodných čísel.

   * **Bolt.cs**: Příklad funkcí bolt, který uchovává počet čísla vysílaných funkcí spout.

     Když vytvoříte projekt, NuGet stáhne nejnovější [SCP.NET balíček](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementace funkcí spout

1. Otevřete **Spout.cs**. Funkcích spouts slouží ke čtení z externího zdroje dat v topologii. Hlavní komponenty pro funkcí spout jsou:

   * **NextTuple**: volány Storm, pokud je povoleno spout emitování nové řazené kolekce členů.

   * **Potvrzení** (pouze pro transakční topologie): zpracovává potvrzení iniciovaná ostatní součásti v topologii pro odeslaný spout řazené kolekce členů. To v úvahu řazené kolekce členů umožňuje spout vědět, že byl úspěšně zpracován podřízené součásti.

   * **Selhání** (pouze pro transakční topologie): zpracovává řazené kolekce členů, které jsou selhání zpracování ostatní součásti v topologii. Implementace metody selhání umožňuje znovu emitování řazenou kolekci členů, takže může být znovu zpracována.

2. Nahraďte obsah **Spout** třída tímto textem: Tento spout náhodně vysílá věty do topologie.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementace funkce bolts

1. Odstraňte existující **Bolt.cs** souboru z projektu.

2. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **novou položku**. V seznamu vyberte **Storm Bolt**a zadejte **Splitter.cs** jako název. Opakujte tento postup vytvoření druhého bolt s názvem **Counter.cs**.

   * **Splitter.cs**: implementuje funkce bolt, které rozdělí věty do jednotlivých slov a vydá nové proud slova.

   * **Counter.cs**: implementuje funkce bolt, které počty jednotlivých slov a vysílá nového datového proudu slova a počty v jednotlivých slov.

     > [!NOTE]
     > Tyto funkce bolts čtení a zápis do datových proudů, ale můžete také použít na funkce bolt ke komunikaci s zdroje jako databázi nebo službě.

3. Otevřete **Splitter.cs**. Ve výchozím nastavení obsahuje pouze jednu metodu: **Execute**. Metoda Execute je volána, když obdrží bolt řazené kolekce členů pro zpracování. Zde si můžete přečíst zpracovat příchozí řazených kolekcí členů a vydávání odchozích řazené kolekce členů.

4. Nahraďte obsah **rozdělovače** třídy následujícím kódem:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Otevřete **Counter.cs**a nahraďte jeho obsah třídy následujícím kódem:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Definovat topologii

Funkcích spouts a funkce bolts jsou uspořádány v grafu, který definuje tok dat mezi součástmi. Pro tuto topologii grafu vypadá takto:

![Diagram jak jsou uspořádány součásti](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Věty jsou nevydává spout a jsou distribuovány do instance rozdělovače bolt. Bolt rozdělovače dělí věty do slova, které jsou distribuovány do bolt čítače.

Vzhledem k tomu, že počet slov trvá místně v instanci čítače, budete chtít Ujistěte se, že určitá slova toku na stejnou instanci bolt čítače. Každá instance uchovává informace o konkrétní slova. Vzhledem k tomu, že bolt rozdělovače udržuje bez stavu, skutečně nezávisle na tom, kterou instanci systému rozdělovače obdrží které věty.

Otevřete **Program.cs**. Je důležité metoda **GetTopologyBuilder**, který se používá k definování topologie, které je odeslána do Storm. Nahraďte obsah **GetTopologyBuilder** implementovat topologii popsané následujícím kódem:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Odeslání topologie

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **odeslání do Storm v HDInsight**.

   > [!NOTE]
   > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se k ta, která obsahuje váš cluster Storm v HDInsight.

2. Vyberte váš cluster Storm v HDInsight z **Storm Cluster** rozevíracího seznamu a potom vyberte **odeslání**. Můžete sledovat, pokud je úspěšné odesílání pomocí **výstup** okno.

3. Když topologii byl úspěšně odeslán, **topologie Storm** pro cluster by se měla objevit. Vyberte **WordCount** topologie ze seznamu zobrazíte informace o spuštěné topologie.

   > [!NOTE]
   > Můžete také zobrazit **topologie Storm** z **Průzkumníka serveru**. Rozbalte položku **Azure** > **HDInsight**, klikněte pravým tlačítkem myši cluster Storm v HDInsight a pak vyberte **topologie Storm zobrazení**.

    Chcete-li zobrazit informace o komponentách v topologii, dvakrát klikněte na komponentu v diagramu.

4. Z **souhrn topologie** zobrazit, klikněte na tlačítko **Kill** k zastavení topologie.

   > [!NOTE]
   > Topologie Storm i nadále spustit, dokud se deaktivovala nebo odstranění clusteru.

## <a name="transactional-topology"></a>Transakční topologie

Předchozí topologie je netransakční. Součásti v topologii neimplementuje funkci k přehrání zprávy. Příklad transakční topologie vytvořte projekt a vyberte **Storm ukázka** jako typ projektu.

Transakční topologie implementovat následující pro podporu opětovného přehrání dat:

* **Ukládání do mezipaměti metadat**: spout musí ukládat metadata o datech vygenerované, takže data můžete načíst a vygenerované znovu, pokud dojde k selhání. Protože je v datech vysílaných ukázku malé, nezpracovaná data pro každý záznam je uloženy ve slovníku pro opakování.

* **Potvrzení**: každý bolt v topologii můžete volat `this.ctx.Ack(tuple)` aby vzali na vědomí, že je úspěšně zpracovala řazené kolekce členů. Pokud všechny funkce bolts acked řazené kolekce členů, `Ack` zavolání metody funkcí spout. `Ack` Metoda umožňuje spout odebrat data, která se ukládá do mezipaměti pro opakování.

* **Selhání**: můžete volat každý bolt `this.ctx.Fail(tuple)` k označení, že zpracování se nezdařilo pro řazené kolekce členů. Selhání rozšíří do `Fail` metoda spout, kde můžete s použitím přehrány řazenou kolekci členů do mezipaměti metadat.

* **Pořadí ID**: při generování řazené kolekce členů, můžete zadat ID jedinečný pořadí. Tato hodnota určuje řazenou kolekci členů pro zpracování opětovného přehrání (Ack a selhání). Například spout v **Storm ukázka** projektu používá následující při generování dat:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Tento kód vysílá řazené kolekce členů, který obsahuje věty do datového proudu výchozí hodnotou pořadí ID obsažené v **lastSeqId**. V tomto příkladu **lastSeqId** se zvýší při každé řazené kolekce členů vygenerované.

Jak je předvedeno v **Storm ukázka** projektu, zda je součást transakční lze nastavit v době běhu v závislosti na konfiguraci.

## <a name="hybrid-topology-with-c-and-java"></a>Hybridní topologie s C# a Java

Nástroje Data Lake pro Visual Studio můžete také vytvářet hybridní topologie, kde jsou některé součásti C# a jiné jsou Java.

Příklad hybridní topologie, vytvořte projekt a vyberte **Storm hybridní ukázka**. Tento typ ukázka ukazuje následující koncepty:

* **Java spout** a **C# bolt**: definovaná v **HybridTopology_javaSpout_csharpBolt**.

    * Transakční verze je definována v **HybridTopologyTx_javaSpout_csharpBolt**.

* **C# spout** a **Java bolt**: definovaná v **HybridTopology_csharpSpout_javaBolt**.

    * Transakční verze je definována v **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > Tato verze také ukazuje, jak použít Clojure kód z textového souboru jako součást Java.


Chcete-li přepnout topologie, která se používá při odeslání projektu, přesunout `[Active(true)]` příkaz do topologie, kterou chcete použít, před odesláním do clusteru.

> [!NOTE]
> Všechny soubory Java, které jsou požadovány jsou uvedeny jako součást tohoto projektu v **JavaDependency** složky.

Při vytváření a odesílání hybridní topologie, zvažte následující:

* Použití **JavaComponentConstructor** k vytvoření instance třídy Java pro funkcích spout nebo funkce bolt.

* Použití **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** k serializaci dat do nebo z komponent v jazyce Java z objekty Java do formátu JSON.

* Při odesílání topologie do serveru, je nutné použít **další konfigurace** možnost zadat **cesty k souborům Java**. Zadaná cesta musí být adresář, který obsahuje soubory JAR obsahující dané třídy jazyka Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Verze SCP.NET 0.9.4.203 zavádí nové třídy a metody speciálně pro práci s funkcí spout centra událostí (spout Java, který čte ze služby Event Hubs). Když vytvoříte topologii, která se používá spout centra událostí, použijte následující metody:

* **EventHubSpoutConfig** – třída: vytvoří objekt, který obsahuje konfiguraci pro součást funkcí spout.

* **TopologyBuilder.SetEventHubSpout** metoda: přidá komponentu spout centra událostí do topologie.

> [!NOTE]
> Je nutné použít **CustomizedInteropJSONSerializer** k serializaci dat vytvářených funkcí spout.

## <a id="configurationmanager"></a>Použití ConfigurationManager

Nepoužívejte **ConfigurationManager** k načtení hodnoty konfigurace z bolt a spout součásti. Díky tomu může způsobit výjimku ukazatele null. Místo toho konfigurace pro váš projekt je předán do topologie Storm jako dvojice klíč a hodnotu v kontextu topologie. Jednotlivé komponenty, které jsou závislé na hodnoty konfigurace musíte je znovu načíst z kontextu během inicializace.

Následující kód ukazuje, jak se načtení těchto hodnot:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Pokud používáte `Get` metoda vrátí instanci komponenty, ujistěte se, že předá obě `Context` a `Dictionary<string, Object>` parametry konstruktoru. Následující příklad je základní `Get` metoda, která správně předává tyto hodnoty:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Postup aktualizace SCP.NET

Poslední verze SCP.NET podporovat upgrade balíčku prostřednictvím balíčku NuGet. Když je k dispozici nové aktualizace, zobrazí se oznámení o upgradu. Ručně zkontrolujte upgrade, postupujte takto:

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.

2. Správce balíčků, vyberte **aktualizace**. Pokud je k dispozici aktualizace, je uvedena. Klikněte na tlačítko **aktualizace** pro balíček k její instalaci.

> [!IMPORTANT]
> Pokud projekt byla vytvořena pomocí dřívější verzi SCP.NET, který NuGet nepoužili, musíte provést následující kroky k aktualizaci na novější verzi:
>
> 1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.
> 2. Pomocí **vyhledávání** pole, vyhledejte a pak přidejte, **Microsoft.SCP.Net.SDK** do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Řešení běžných problémů s topologie

### <a name="null-pointer-exceptions"></a>Výjimky ukazatele Null.

Při použití topologie C# s clusterem HDInsight se systémem Linux, funkce bolt a spout součásti, které používají **ConfigurationManager** číst nastavení konfigurace v době běhu může vrátit výjimky ukazatele null.

Konfigurace pro váš projekt je předán do topologie Storm jako dvojice klíč a hodnotu v kontextu topologie. Se dá načíst z objektu slovník, který je předán u součástí při jejich inicializaci.

Další informace najdete v tématu [ConfigurationManager](#configurationmanager) část tohoto dokumentu.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Při použití topologie C# s clusterem HDInsight se systémem Linux, může dojít k následující chybě:

    System.TypeLoadException: Failure has occurred while loading a type.

K této chybě dojde, pokud použijete binární soubor, který není kompatibilní s verzí rozhraní .NET, která podporuje Mono.

Pro clustery HDInsight se systémem Linux Ujistěte se, že váš projekt používá binární soubory zkompilovaném pro rozhraní .NET 4.5.

### <a name="test-a-topology-locally"></a>Testování topologii místně

I když je snadno nasadit topologii do clusteru, v některých případech může je potřeba provést testování topologii místně. Pomocí následujících kroků spustit a otestovat topologie příklad v tomto kurzu místně ve vašem vývojovém prostředí.

> [!WARNING]
> Místní testování funguje výhradně u basic, C# – pouze topologie. Nemůžete použít místní testování pro hybridní topologie nebo topologie, které používají víc datových proudů.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **vlastnosti**. Ve vlastnostech projektu změnit **výstupní typ** k **konzolové aplikace**.

    ![Snímek obrazovky vlastností projektu, se zvýrazněným typem výstupu](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Mějte na paměti, chcete-li změnit **výstupní typ** zpět na **knihovny tříd** před nasazením topologie do clusteru.

2. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a pak vyberte **přidat** > **novou položku**. Vyberte **třída**a zadejte **LocalTest.cs** jako název třídy. Nakonec klikněte na **přidat**.

3. Otevřete **LocalTest.cs**a přidejte následující **pomocí** příkaz v horní části:

    ```csharp
    using Microsoft.SCP;
    ```

4. Použít následující kód jako obsah **LocalTest** třídy:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Pozorně si přečíst komentáře kódu. Tento kód používá **LocalContext** spuštění součásti v vývojového prostředí a přetrvává datový proud mezi součástmi k textovým souborům na místní disk.

1. Otevřete **Program.cs**a přidejte následující **hlavní** metoda:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Uložte změny a pak klikněte na tlačítko **F5** nebo vyberte **ladění** > **spustit ladění** a spusťte projekt. Okno konzoly by měla zobrazovat a stavu protokolu jako průběhu testů. Když **dokončení testů** se zobrazí, stisknutím libovolné klávesy zavřete toto okno.

3. Použití **Průzkumníka Windows** vyhledejte adresář, který obsahuje projektu. Příklad: **C:\Users\<vaše_uživatelské_jméno > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. V tomto adresáři otevřete **Bin**a potom klikněte na **ladění**. Textové soubory, které byly vytvořeny při testy došlo měli vidět: sentences.txt, counter.txt a splitter.txt. Otevřete každý textový soubor a kontrolovat data.

   > [!NOTE]
   > Řetězec dat trvá jako pole desetinných míst v těchto souborech. Například \[[97,103,111]] v **splitter.txt** soubor je slovo *a*.

> [!NOTE]
> Nastavte **typ projektu** zpět na **knihovny tříd** před nasazením Storm v clusteru HDInsight.

### <a name="log-information"></a>Informace o protokolu

Můžete snadno protokolovat informace ze součásti vaší topologie pomocí `Context.Logger`. Například následující příkaz vytvoří položku informační protokolu:

```csharp
Context.Logger.Info("Component started");
```

Zaznamenané informace lze zobrazit z **protokol služby Hadoop**, který se nachází v **Průzkumníka serveru**. Rozbalte položku pro váš cluster Storm v HDInsight a pak rozbalte **protokol služby Hadoop**. Nakonec vyberte soubor protokolu, chcete-li zobrazit.

> [!NOTE]
> Protokoly jsou uloženy v účtu úložiště Azure, který je používán clusteru. K zobrazení protokolů v sadě Visual Studio, musíte se přihlásit k předplatnému Azure, který vlastní účet úložiště.

### <a name="view-error-information"></a>Informace o chybě zobrazení

Chcete-li zobrazit chyby, ke kterým došlo v spuštěné topologie, použijte následující kroky:

1. Z **Průzkumníka serveru**, klikněte pravým tlačítkem myši cluster Storm v HDInsight a vyberte **topologie Storm zobrazení**.

2. Pro **Spout** a **Bolts**, **poslední chyba** sloupec obsahuje informace o poslední chybě.

3. Vyberte **Spout Id** nebo **Bolt Id** pro součást, která obsahuje chybu uvedené. Na stránce podrobností, který je zobrazený, další chyba je uvedena informace ve **chyby** v dolní části stránky.

4. Chcete-li získat další informace, vyberte **Port** z **vykonavatelů** části stránky, naleznete v protokolu Storm pracovního procesu v posledních několika minutách.

### <a name="errors-submitting-topologies"></a>Chyby odesílání topologie

Pokud narazíte na chyby odesílání topologie do HDInsight, můžete nějakého najít protokoly pro serverové komponenty, které zpracovávají topologie odeslání na clusteru HDInsight. Chcete-li získat tyto protokoly, použijte následující příkaz z příkazového řádku:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Nahraďte __sshuser__ pomocí uživatelského účtu SSH pro cluster. Nahraďte __clustername__ s názvem clusteru HDInsight. Další informace o používání `scp` a `ssh` s HDInsight, najdete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Odesílání může selhat z několika důvodů:

* JDK není nainstalována nebo není v cestě.
* Požadované závislosti Java nejsou v odesílání zahrnuté.
* Nekompatibilní závislosti.
* Duplicitní názvy topologie.

Pokud `hdinsight-scpwebapi.out` protokol obsahuje `FileNotFoundException`, může to být způsobeno následující podmínky:

* Sadu JDK není v cestě na vývojovém prostředí. Ověřte, zda je nainstalován sadu JDK v vývojového prostředí a že `%JAVA_HOME%/bin` v cestě.
* Chybí závislost Java. Ujistěte se, že všechny požadované .jar soubory jsou včetně jako součást odesílání.

## <a name="next-steps"></a>Další postup

Příklad zpracování dat ze služby Event Hubs naleznete v části [zpracovat události z Azure Event Hubs se Storm v HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Příklad topologie C#, která rozdělí datový proud do různých datových proudů, naleznete v části [C# Storm příklad](https://github.com/Blackmist/csharp-storm-example).

Chcete-li zjistit další informace o vytváření topologie C#, přečtěte si téma [Githubu](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Pro další způsoby, jak pracovat s HDInsight a další Storm v HDInsight ukázky najdete v následujících dokumentech:

**Microsoft SCP.NET**

* [Průvodce programováním spojovací bod služby](apache-storm-scp-programming-guide.md)

**Apache Storm v HDInsight**

* [Nasazení a monitorování topologie s Apache Storm v HDInsight](apache-storm-deploy-monitor-topology.md)
* [Příklad topologií pro Storm v HDInsight](apache-storm-example-topology.md)

**Apache Hadoop v HDInsight**

* [Použijte Hive s Hadoop v HDInsight](../hadoop/hdinsight-use-hive.md)
* [Použijte Pig s Hadoop v HDInsight](../hadoop/hdinsight-use-pig.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase v HDInsight**

* [Začínáme s HBase v HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
