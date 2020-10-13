---
title: Apache Storm topologie se sadou Visual Studio a C# – Azure HDInsight
description: Naučte se vytvářet topologie v jazyce C#. Vytvořte topologii počtu slov v aplikaci Visual Studio pomocí nástrojů Hadoop pro Visual Studio.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 271f62625433a6651ba0e3230a62be51e5147f3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000188"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Vývoj topologií v jazyce C# pro Apache Storm pomocí nástrojů Data Lake pro Visual Studio

Naučte se, jak vytvořit topologii Apache Storm v jazyce C# pomocí nástrojů Azure Data Lake (Apache Hadoop) pro Visual Studio. Tento dokument vás provede procesem vytvoření projektu zaplavení v aplikaci Visual Studio, jeho otestováním lokálně a nasazením do Apache Storm v clusteru Azure HDInsight.

Naučíte se také, jak vytvořit hybridní topologie, které používají komponenty C# a Java.

Topologie C# používají .NET 4,5 a ke spuštění v clusteru HDInsight použijte mono. Informace o možných nekompatibilitách najdete v tématu [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/). Chcete-li použít topologii jazyka C#, je nutné aktualizovat `Microsoft.SCP.Net.SDK` balíček NuGet používaný projektem na verzi 0.10.0.6 nebo novější. Verze balíčku se zároveň musí shodovat s hlavní verzí Stormu nainstalovanou ve službě HDInsight.

| Verze HDInsight | Verze Apache Storm | Verze SCP.NET | Výchozí verze mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0. x | 0.10.0. x | 3.2.8 |
| 3,5 | 1.0.2. x | 1.0.0. x | 4.2.1 |
| 3,6 | 1.1.0. x | 1.0.0. x | 4.2.8 |

## <a name="prerequisite"></a>Požadavek

Cluster Apache Storm v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) **a výběr funkce** pro **typ clusteru**.

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

Můžete vyvíjet topologie jazyka C# pomocí SCP.NET pomocí sady [Visual Studio](https://visualstudio.microsoft.com/downloads/). Zde uvedené pokyny používají sadu Visual Studio 2019, ale můžete použít také starší verze sady Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Nainstalovat Data Lake nástroje pro Visual Studio

Chcete-li nainstalovat nástroje Data Lake Tools for Visual Studio, postupujte podle kroků v části [Začínáme používat data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Nainstalovat Java

Při odeslání topologie negenerovaného toku ze sady Visual Studio vygeneruje SCP.NET soubor zip, který obsahuje topologii a závislosti. Jazyk Java slouží k vytvoření těchto souborů zip, protože používá formát, který je více kompatibilní s clustery se systémem Linux.

1. Do vývojového prostředí nainstalujte sadu Java Developer Kit (JDK) 7 nebo novější. Oracle JDK můžete získat od [Oracle](https://openjdk.java.net/). Můžete také použít [jiné distribuce Java](/java/azure/jdk/).

2. Nastavte `JAVA_HOME` proměnnou prostředí na adresář, který obsahuje Java.

3. Nastavte `PATH` proměnnou prostředí tak, aby zahrnovala `%JAVA_HOME%\bin` adresář.

Můžete sestavit a spustit následující konzolovou aplikaci v jazyce C# a ověřit, zda jsou správně nainstalovány jazyky Java a JDK:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Šablony Apache Storm

Nástroje pro Data Lake pro Visual Studio poskytují následující šablony:

| Typ projektu | Demonstruje |
| --- | --- |
| Aplikace s více aplikacemi |Prázdný projekt topologie naplnění. |
| Ukázka funkce pro vyplavení Azure SQL Writer |Jak zapisovat do Azure SQL Database. |
| Ukázka Azure Cosmos DBho čtecího modulu pro čtení |Jak číst z Azure Cosmos DB. |
| Ukázka funkce pro zápis Azure Cosmos DBho zápisu |Jak zapisovat do Azure Cosmos DB. |
| Ukázka čtečky EventHub pro EventHub |Jak číst z Azure Event Hubs. |
| Ukázka zapisovače nástroje pro vyplavení EventHub |Jak zapisovat do Azure Event Hubs. |
| Ukázka čtecího modulu pro vyplavování HBA |Jak číst z adaptérů HBA v clusterech HDInsight. |
| Ukázka zapisovače pro vyplavování HBA |Jak zapisovat do adaptérů HBA v clusterech HDInsight. |
| Hybridní ukázka s více podprocesy |Jak používat komponentu Java. |
| Ukázka obplavení |Základní topologie počtu slov. |

> [!WARNING]  
> Ne všechny šablony pracují se systémem Linux HDInsight. Balíčky NuGet používané šablonami nemusí být kompatibilní s mono. Pokud chcete zjistit možné problémy, přečtěte si téma [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/) a použijte [analyzátor přenositelnosti .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

V krocích v tomto dokumentu použijete k vytvoření topologie typ projektu základní aplikace pro zaplavení.

### <a name="apache-hbase-templates"></a>Šablony Apache HBA

Šablony pro čtečku a zapisovače HBA používají adaptéry HBA REST API, ne adaptéry Java API pro komunikaci s HBA v clusteru HDInsight.

### <a name="eventhub-templates"></a>Šablony EventHub

> [!IMPORTANT]  
> Komponenta EventHub Spout na bázi Java, která je součástí šablony čtečky EventHub, nemusí fungovat se zaplavou ve službě HDInsight verze 3,5 nebo novější. Aktualizovaná verze této součásti je k dispozici na [GitHubu](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Příklad topologie, která používá tuto součást a pracuje se zachováním v HDInsight 3,5, najdete v tématu [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Vytvoření topologie jazyka C#

Vytvoření projektu topologie v jazyce C# v aplikaci Visual Studio:

1. Otevřete sadu Visual Studio.

1. V okně **Start** vyberte **vytvořit nový projekt**.

1. V okně **vytvořit nový projekt** přejděte na a vyberte možnost **aplikace**pro zaplavení a pak vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte **název projektu** *WORDCOUNT*, přejděte na nebo vytvořte cestu k adresáři **umístění** pro projekt a pak vyberte **vytvořit**.

    ![Zaplavení aplikace, konfigurace dialogového okna Nový projekt, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Po vytvoření projektu byste měli mít následující soubory:

* *Program.cs*: definice topologie pro váš projekt. Ve výchozím nastavení se vytvoří výchozí topologie, která se skládá z jednoho Spout a jednoho šroubu.

* *Spout.cs*: příklad Spout, který generuje náhodná čísla.

* *Bolt.cs*: příklad typu, který udržuje počet čísel generovaných Spout.

Při vytváření projektu NuGet stáhne nejnovější [balíček SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Implementace rozhraní Spout

Dále přidejte kód pro Spout, který se používá ke čtení dat v topologii z externího zdroje. Tento Spout náhodně generuje větu do topologie.

1. Otevřete *Spout.cs*. Hlavní součásti pro Spout jsou:

   * `NextTuple`: Voláno pomocí metody], když má Spout povoleno generovat nové řazené kolekce členů.

   * `Ack` (pouze transakční topologie): zpracovává potvrzovací potvrzení spuštěná jinými součástmi v topologii pro řazené kolekce členů odeslané z Spout. Potvrzením řazené kolekce členů umožní Spout zjistit, že byl úspěšně zpracován pomocí součástí podřízené součásti.

   * `Fail` (pouze transakční topologie): zpracovává řazené kolekce členů, které zpracovávají selhání jiných komponent v topologii. Implementace `Fail` metody umožňuje znovu vygenerovat řazenou kolekci členů, aby ji bylo možné znovu zpracovat.

2. Obsah třídy nahraďte `Spout` následujícím textem:

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

### <a name="implement-the-bolts"></a>Implementace šrouby

Nyní v tomto příkladu vytvořte dvě šrouby:

1. Odstraňte existující soubor *Bolt.cs* z projektu.

2. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte možnost **Přidat**  >  **novou položku**. V seznamu vyberte možnost **přešroubování**a jako název zadejte *Splitter.cs* . V kódu nového souboru změňte název oboru názvů na `WordCount` . Pak tento postup opakujte, abyste vytvořili druhý šroub s názvem *Counter.cs*.

   * *Splitter.cs*: implementuje šroub, který rozdělí věty na jednotlivá slova a vygeneruje nový proud slov.

   * *Counter.cs*: implementuje šroub, který počítá každé slovo, a vygeneruje nový proud slov a počet pro každé slovo.

     > [!NOTE]  
     > Tyto šrouby čtou a zapisují do datových proudů, ale můžete také použít šroub ke komunikaci se zdroji, jako je databáze nebo služba.

3. Otevřete *Splitter.cs*. Ve výchozím nastavení má pouze jednu metodu: `Execute` . `Execute`Metoda je volána, když šroub obdrží řazenou kolekci členů ke zpracování. Tady můžete číst a zpracovávat příchozí řazené kolekce členů a generovat odchozí řazené kolekce členů.

4. Obsah třídy nahraďte `Splitter` následujícím kódem:

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

5. Otevřete *Counter.cs*a nahraďte obsah třídy následujícím kódem:

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

### <a name="define-the-topology"></a>Definování topologie

Spoutů a šrouby jsou uspořádány do grafu, který definuje způsob toku dat mezi komponentami. V této topologii je graf následující:

![Diagram uspořádání komponent Spout a šroubů, topologie rozplavení](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Spout vygeneruje věty, které jsou distribuovány do instancí rozdělovače. Šroubce přerušuje věty na slova, která jsou distribuována do čítače čítačů.

Vzhledem k tomu, že instance čítače obsahuje počet slov místně, chcete zajistit, aby určitá slova byla v toku ke stejné instanci šroubu čítače. Každá instance uchovává záznam o konkrétních slovech. Vzhledem k tomu, že rozdělovač oddělovače udržuje žádný stav, nezáleží na tom, která instance rozdělovače obdrží tuto větu.

Otevřete *program.cs*. Důležitou metodou je `GetTopologyBuilder` , která se používá k definování topologie, která je odeslána do přeplavení. Nahraďte obsah `GetTopologyBuilder` následujícím kódem pro implementaci výše popsané topologie:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

## <a name="submit-the-topology"></a>Odeslat topologii

Nyní jste připraveni odeslat topologii do clusteru HDInsight.

1. Přejděte k **zobrazení**  >  **Průzkumník serveru**.

1. Klikněte pravým tlačítkem myši na **Azure**, vyberte **připojit k Microsoft Azure předplatnému...** a dokončete proces přihlašování.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Odeslat pro**zaplavení v HDInsight.

1. V dialogovém okně **Odeslat topologii** v rozevíracím seznamu cluster nenáročného **clusteru** zvolte své zaplavení na clusteru HDInsight a pak vyberte **Odeslat**. Můžete ověřit, zda je odeslání úspěšné, zobrazením podokna **výstup** .

    Po úspěšném odeslání topologie by se měla zobrazit okno **zobrazení topologií** vyplavení pro daný cluster. Vyberte topologii **WORDCOUNT** ze seznamu a zobrazte informace o spuštěné topologii.

    ![Okno zobrazení topologie navýšení, cluster HDInsight, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > **Topologie** **přePrůzkumník serveru**můžete zobrazit také z. Rozbalte **Azure**  >  **HDInsight**, klikněte pravým tlačítkem na cluster HDInsight a pak zvolte **Zobrazit topologie**přeplavování.

    Chcete-li zobrazit informace o součástech v topologii, vyberte součást v diagramu.

1. V části **Souhrn topologie** vyberte možnost **Kill** a zastavte topologii.

    > [!NOTE]  
    > Topologie zaplavování se nadále spouštějí, dokud nejsou deaktivovány, nebo když se cluster odstraní.

## <a name="transactional-topology"></a>Transakční topologie

Předchozí topologie je jiná než transakční. Komponenty v topologii neimplementují funkce pro přehrávání zpráv. Příklad transakční topologie získáte tak, že vytvoříte projekt a jako typ projektu vyberete možnost Nevytvářet **vzorek** .

Transakční topologie implementují následující, aby podporovaly přehrávání dat:

* **Ukládání metadat do mezipaměti**: Spout musí ukládat metadata o vysílaných datech, aby se data mohla načíst a emitovat znovu, pokud dojde k selhání. Vzhledem k tomu, že data vygenerovaná ukázkou jsou malá, jsou nezpracovaná data pro každou řazenou kolekci členů uložena ve slovníku pro opětovné přehrání.

* **ACK**: každé pole topologie může volat `this.ctx.Ack(tuple)` , aby bylo možné potvrdit, že úspěšně zpracovalo řazenou kolekci členů. Když všechny šrouby potvrdí řazenou kolekci členů, `Ack` vyvolá se metoda Spout. `Ack`Metoda umožňuje Spout odebrat data, která byla uložena do mezipaměti pro opětovné přehrání.

* **Selhání**: každé šroubové volání může `this.ctx.Fail(tuple)` signalizovat, že zpracování pro řazené kolekce členů selhalo. Selhání se šíří do `Fail` metody Spout, kde je možné znovu přehrát řazenou kolekci členů pomocí metadat uložených v mezipaměti.

* **ID sekvence**: při generování řazené kolekce členů lze zadat jedinečné ID sekvence. Tato hodnota určuje řazenou kolekci členů pro zpracování opětovného přehrání ( `Ack` a `Fail` ). Například Spout v projektu s **ukázkovým** zaplavení používá při vygenerování dat následující volání metody:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Tento kód vygeneruje řazenou kolekci členů, která obsahuje větu k výchozímu datovému proudu s hodnotou ID sekvence obsaženou v `lastSeqId` . V tomto příkladu `lastSeqId` se zvyšuje pro každou vydanou řazenou kolekci členů.

Jak je znázorněno v projektu **ukázkového** navýšení, zda je součást transakční, lze nastavit za běhu na základě konfigurace.

## <a name="hybrid-topology-with-c-and-java"></a>Hybridní topologie s C# a Java

Pomocí nástrojů Data Lake pro Visual Studio můžete také vytvářet hybridní topologie, kde některé součásti jsou C# a jiné jsou Java.

Příklad hybridní topologie získáte tak, že vytvoříte projekt a vyberete možnost navýšit **hybridní vzorek**. Tento vzorový typ předvádí následující koncepty:

* Jazyky **Java Spout** a **C#**: definováno ve `HybridTopology_javaSpout_csharpBolt` třídě.

  Transakční verze je definována ve `HybridTopologyTx_javaSpout_csharpBolt` třídě.

* **C# Spout** a **Java**– definice ve `HybridTopology_csharpSpout_javaBolt` třídě.

  Transakční verze je definována ve `HybridTopologyTx_csharpSpout_javaBolt` třídě.

  > [!NOTE]  
  > Tato verze také ukazuje, jak použít kód Clojure z textového souboru jako součást Java.

Chcete-li přepnout topologii, která se používá při odeslání projektu, přesuňte `[Active(true)]` příkaz do topologie, kterou chcete použít, před odesláním do clusteru.

> [!NOTE]  
> Všechny soubory Java, které jsou požadovány, jsou k dispozici jako součást tohoto projektu ve složce *JavaDependency* .

Při vytváření a odesílání hybridní topologie Vezměte v úvahu následující skutečnosti:

* Slouží `JavaComponentConstructor` k vytvoření instance třídy Java pro Spout nebo šroub.

* Slouží `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` k serializaci dat do komponent Java z objektů Java do formátu JSON nebo z nich.

* Při odesílání topologie na server je nutné použít možnost **dalších konfigurací** k určení **cest k souborům Java**. Zadaná cesta by měla být adresář, který obsahuje soubory JAR obsahující vaše třídy Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET verze 0.9.4.203 zavádí novou třídu a metodu specificky pro práci s centrem událostí Spout (Java Spout, který čte z Event Hubs). Když vytváříte topologii, která používá centrum událostí Spout (například pomocí **ukázkové šablony čtečky EventHub** , použijte následující rozhraní API):

* `EventHubSpoutConfig` Třída: vytvoří objekt, který obsahuje konfiguraci pro komponentu Spout.

* `TopologyBuilder.SetEventHubSpout` Metoda: přidá do topologie komponentu Spout centra událostí.

> [!NOTE]  
> `CustomizedInteropJSONSerializer`Ke serializaci dat vytvořených v Spout je nutné stále použít.

## <a name="use-configurationmanager"></a>Použití ConfigurationManager

Nepoužívejte **ConfigurationManager** k načtení hodnot konfigurace ze součástí šroubů a Spout. V takovém případě může dojít k výjimce ukazatele s hodnotou null. Místo toho předejte konfiguraci pro váš projekt do topologie pro zaplavení jako dvojici klíč a hodnota v kontextu topologie. Každá komponenta, která závisí na hodnotách konfigurace, musí být při inicializaci načítána z kontextu.

Následující kód ukazuje, jak načíst tyto hodnoty:

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
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Použijete-li `Get` metodu pro vrácení instance komponenty, je nutné zajistit, aby `Context` `Dictionary<string, Object>` do konstruktoru předala parametry a. Následující příklad je základní `Get` metoda, která správně předává tyto hodnoty:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Postup aktualizace SCP.NET

Nejnovější verze SCP.NET podporují upgrade balíčku prostřednictvím NuGet. Když je k dispozici nová aktualizace, obdržíte oznámení o upgradu. Chcete-li provést ruční kontrolu upgradu, postupujte podle následujících kroků:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.

2. Ve Správci balíčků vyberte **aktualizace**. Pokud je k dispozici aktualizace pro balíček podpory SCP.NET, je uvedena v seznamu. Vyberte **aktualizace** balíčku a potom v dialogovém okně **Náhled změn** vyberte **OK** a nainstalujte ho.

> [!IMPORTANT]  
> Pokud byl projekt vytvořen pomocí starší verze SCP.NET, která nepoužívala NuGet, je nutné provést následující kroky, aby se aktualizovala na novější verzi:
>
> 1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.
> 2. Pomocí **vyhledávacího** pole vyhledejte a poté přidejte `Microsoft.SCP.Net.SDK` do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Řešení běžných potíží s topologiemi

### <a name="null-pointer-exceptions"></a>Výjimky ukazatele s hodnotou null

Pokud používáte topologii jazyka C# s clusterem HDInsight se systémem Linux, součásti šroubů a Spout, které používají **ConfigurationManager** ke čtení konfiguračních nastavení za běhu, mohou vracet výjimky ukazatele s hodnotou null.

Konfigurace pro váš projekt je předána do topologie přetvoření jako dvojice klíč a hodnota v kontextu topologie. Dá se načíst z objektu Dictionary, který se předává komponentám při inicializaci.

Další informace najdete v části [použití ConfigurationManager](#use-configurationmanager) tohoto dokumentu.

### <a name="systemtypeloadexception"></a>System. TypeLoadException

Při použití topologie jazyka C# s clusterem HDInsight se systémem Linux může docházet k následující chybě:

`System.TypeLoadException: Failure has occurred while loading a type.`

K této chybě dochází, pokud použijete binární soubor, který není kompatibilní s verzí rozhraní .NET, kterou mono podporuje.

U clusterů HDInsight se systémem Linux se ujistěte, že váš projekt používá binární soubory kompilované pro .NET 4,5.

### <a name="test-a-topology-locally"></a>Místní otestování topologie

I když je možné topologii nasadit do clusteru, může být v některých případech potřeba topologii otestovat místně. Následující postup použijte k místnímu spuštění a otestování ukázkové topologie v tomto článku ve vývojovém prostředí.

> [!WARNING]  
> Místní testování funguje pouze pro základní topologie pouze pro C#. Místní testování nelze použít pro hybridní topologie nebo topologie, které používají více datových proudů.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **vlastnosti**. Ve vlastnostech projektu. Pak změňte **Typ výstupu** na **konzolovou aplikaci**.

   ![Aplikace HDInsight-zaplavení, vlastnosti projektu, typ výstupu](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Nezapomeňte změnit **Typ výstupu** zpátky do **knihovny tříd** před nasazením topologie do clusteru.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte možnost **Přidat**  >  **novou položku**. Vyberte **Třída**a jako název třídy zadejte *LocalTest.cs* . Nakonec vyberte **Přidat**.

1. Otevřete *LocalTest.cs*a přidejte následující `using` příkaz v horní části:

    ```csharp
    using Microsoft.SCP;
    ```

1. Jako obsah třídy použijte následující kód `LocalTest` :

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

    Věnujte si chvilku, abyste si přečetli komentáře ke kódu. Tento kód používá `LocalContext` ke spuštění komponent ve vývojovém prostředí. Ukládá datový proud mezi součástmi do textových souborů na místním disku.

1. Otevřete *program.cs*a do metody přidejte následující kód `Main` :

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

1. Uložte změny a pak vyberte **F5** nebo zvolte **ladění**  >  **Spustit ladění** a spusťte tak projekt. Mělo by se zobrazit okno konzoly a log status jako průběh testů. Když `Tests finished` se zobrazí, vyberte libovolnou klávesu pro zavření okna.

1. Pomocí **Průzkumníka Windows** vyhledejte adresář, který obsahuje váš projekt. (Například: *C: \\ Users \\ \<your_user_name> \\ source \\ úložišť \\ WORDCOUNT \\ WORDCOUNT*.) Pak v tomto adresáři otevřete *přihrádku*a potom vyberte *ladit*. Měli byste vidět textové soubory, které byly vytvořeny při spuštění testů: *sentences.txt*, *counter.txt*a *splitter.txt*. Otevřete každý textový soubor a zkontrolujte data.

   > [!NOTE]  
   > Řetězcová data v těchto souborech přetrvají jako pole desítkových hodnot. Například `[[97,103,111]]` v souboru **splitter.txt** představuje *před*slovem.

> [!NOTE]  
> Nezapomeňte nastavit **typ projektu** zpět na **knihovnu tříd** ve vlastnostech projektu před nasazením na šíření v clusteru HDInsight.

### <a name="log-information"></a>Informace protokolu

Informace ze svých komponent topologie můžete snadno protokolovat pomocí `Context.Logger` . Například následující příkaz vytvoří informační položku protokolu:

`Context.Logger.Info("Component started");`

Protokolované informace můžete zobrazit z **protokolu služby Hadoop**, který najdete v **Průzkumník serveru**. Rozbalte položku pro vaše vyplavení v clusteru HDInsight a potom rozbalte **protokol služby Hadoop**. Nakonec vyberte soubor protokolu, který chcete zobrazit.

> [!NOTE]  
> Protokoly se ukládají do účtu služby Azure Storage, který používá váš cluster. Chcete-li zobrazit protokoly v aplikaci Visual Studio, musíte se přihlásit k předplatnému Azure, které vlastní účet úložiště.

### <a name="view-error-information"></a>Zobrazit informace o chybě

Chcete-li zobrazit chyby, ke kterým došlo ve spuštěné topologii, použijte následující postup:

1. V **Průzkumník serveru**klikněte pravým tlačítkem na cluster se systémem HDInsight a vyberte **Zobrazit topologie**přeplavení.

   Sloupec **Poslední chyba** pro **Spout** a **šrouby**obsahuje informace o Poslední chybě.

2. Vyberte **ID Spout** nebo **ID šroubu** pro komponentu, u které je uvedená chyba. Stránka Podrobnosti obsahuje další informace o chybě v části **chyby** v dolní části stránky.

3. Pokud chcete získat další informace, vyberte **port** z části **vykonavatelé** na stránce, abyste viděli protokol pracovního procesu pro více než posledních pár minut.

### <a name="errors-submitting-topologies"></a>Chyby při odesílání topologií

Pokud provedete chyby při odesílání topologie do HDInsight, můžete najít protokoly pro serverové komponenty, které zpracovávají odesílání topologie v clusteru HDInsight. Chcete-li stáhnout tyto protokoly, použijte následující příkaz z příkazového řádku:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Nahraďte *sshuser* uživatelským účtem SSH pro cluster. Položku *název_clusteru* nahraďte názvem clusteru HDInsight. Další informace o používání `scp` a `ssh` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Odesílání může selhat z několika důvodů:

* JDK není nainstalován nebo není v cestě.
* Požadované závislosti Java nejsou zahrnuté do odeslání.
* Závislosti jsou nekompatibilní.
* Názvy topologie jsou duplicitní.

Pokud soubor protokolu *HDInsight-scpwebapi. out* obsahuje `FileNotFoundException` výjimku, může být výjimka způsobena následujícími podmínkami:

* JDK není v cestě k vývojovému prostředí. Ověřte, že je JDK nainstalovaný ve vývojovém prostředí a že `%JAVA_HOME%/bin` je v cestě.
* Chybí závislost Java. Ujistěte se, že jako součást odeslání budete zahrnovat všechny požadované soubory. jar.

## <a name="next-steps"></a>Další kroky

Příklad zpracování dat z Event Hubs najdete v tématu [zpracování událostí z Azure Event Hubs se](apache-storm-develop-csharp-event-hub-topology.md)zaplavou v HDInsight.

Příklad topologie jazyka C#, která rozdělí datový proud na více datových proudů, naleznete v tématu [příklad prostředí c#](https://github.com/Blackmist/csharp-storm-example).

Další informace o vytváření topologií v jazyce C# najdete v tématu [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Další způsoby, jak pracovat se službou HDInsight a větším využitím ukázek HDInsight, najdete v následujících dokumentech:

**Microsoft SCP.NET**

* [Průvodce programováním SCP pro Apache Storm ve službě Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm v HDInsight**

* [Nasazení a Správa topologií Apache Storm v Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Příklady Apache Storm topologie v Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop ve službě HDInsight**

* [Co je Apache Hive a HiveQL ve službě Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Použití MapReduce v Apache Hadoopu ve službě HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase ve službě HDInsight**

* [Použití Apache HBase v Azure HDInsightu](../hbase/apache-hbase-tutorial-get-started-linux.md)
