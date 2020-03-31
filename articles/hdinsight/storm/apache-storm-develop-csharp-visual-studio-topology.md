---
title: Topologie Apache Storm s Visual Studio a C# - Azure HDInsight
description: Přečtěte si, jak vytvořit topologii Storm v c#. Vytvořte topologii počtu slov v sadě Visual Studio pomocí nástrojů Hadoop pro Visual Studio.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612287"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Vývoj topologií Jazyka C# pro Apache Storm pomocí nástrojů Data Lake pro Visual Studio

Zjistěte, jak vytvořit topologii C# Apache Storm pomocí nástrojů Azure Data Lake (Apache Hadoop) pro Visual Studio. Tento dokument vás provede procesem vytváření projektu Storm ve Visual Studiu, testování místně a jeho nasazení do clusteru Apache Storm v Azure HDInsight.

Dozvíte se také, jak vytvořit hybridní topologie, které používají komponenty Jazyka C# a Java.

Topologií jazyka C# používá rozhraní .NET 4.5 a ke spuštění v clusteru HDInsight použijte mono. Informace o potenciální nekompatibilitě naleznete v [tématu Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/). Chcete-li použít topologii jazyka C#, je nutné aktualizovat balíček `Microsoft.SCP.Net.SDK` NuGet používaný projektem na verzi 0.10.0.6 nebo novější. Verze balíčku se zároveň musí shodovat s hlavní verzí Stormu nainstalovanou ve službě HDInsight.

| VERZE HDInsight | Apache Storm verze | SCP.NET verze | Výchozí verze Mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Požadavek

Cluster Apache Storm na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **Storm** pro **typ clusteru**.

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

Topologií jazyka C# můžete vyvíjet pomocí SCP.NET pomocí [sady Visual Studio](https://visualstudio.microsoft.com/downloads/). Pokyny zde používají Visual Studio 2019, ale můžete také použít starší verze sady Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů datového jezera pro Visual Studio

Chcete-li nainstalovat nástroje Data Lake pro Visual Studio, postupujte podle pokynů v [části Začínáme používat nástroje Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Instalace Javy

Při odeslání topologie Storm z visual studia, SCP.NET generuje soubor zip, který obsahuje topologii a závislosti. Java se používá k vytvoření těchto zip souborů, protože používá formát, který je více kompatibilní s Linux-založené clustery.

1. Nainstalujte do vývojového prostředí java vývojářskou sadu (JDK) 7 nebo novější. Sada Oracle JDK můžete získat od společnosti [Oracle](https://openjdk.java.net/). Můžete také použít [jiné distribuce Java](/java/azure/jdk/).

2. Nastavte `JAVA_HOME` proměnnou prostředí do adresáře, který obsahuje jazyk Java.

3. Nastavte `PATH` proměnnou prostředí `%JAVA_HOME%\bin` tak, aby zahrnovala adresář.

Můžete vytvořit a spustit následující konzolovou aplikaci C# a ověřit, zda jsou java a JDK správně nainstalovány:

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

Nástroje Data Lake pro Visual Studio poskytují následující šablony:

| Typ projektu | Demonstruje |
| --- | --- |
| Aplikace Storm |Prázdný projekt topologie Storm. |
| Ukázka zapisovače SQL Storm Azure |Jak psát do Azure SQL Database. |
| Ukázka čtečky DB Storm Cosmos |Jak číst z Azure Cosmos DB. |
| Ukázka zapisovače Storm Cosmos DB |Jak zapisovat do Azure Cosmos DB. |
| Ukázka čtečky Storm EventHub |Jak číst z Azure Event Hubs. |
| Ukázka zapisovače Storm EventHub |Jak psát do Azure Event Hubs. |
| Ukázka čtečky Storm HBase |Jak číst z HBase na HDInsight clusterech. |
| Ukázka zapisovače Storm HBase |Jak psát do HBase na HDInsight clusterech. |
| Storm Hybridní vzorek |Jak používat komponentu Java. |
| Ukázka bouře |Základní topologie počtu slov. |

> [!WARNING]  
> Ne všechny šablony fungují s HdInsight založeným na Linuxu. Balíčky NuGet používané šablonami nemusí být kompatibilní s Mono. Chcete-li zjistit potenciální problémy, [přečtěte si informace o kompatibilitě mono](https://www.mono-project.com/docs/about-mono/compatibility/) a použijte [nástroj .NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

V krocích v tomto dokumentu použijete základní typ projektu aplikace Storm k vytvoření topologie.

### <a name="apache-hbase-templates"></a>Šablony Apache HBase

Šablony čtečky a zapisovače HBase používají ke komunikaci s hbase v clusteru HDInsight rozhraní MBase REST, nikoli rozhraní HBase Java API.

### <a name="eventhub-templates"></a>Šablony EventHub

> [!IMPORTANT]  
> Komponenta hubice EventHub založená na jazyce Java, která je součástí šablony EventHub Reader, nemusí fungovat s Stormem na HDInsight verze 3.5 nebo novější. Aktualizovaná verze této součásti je k dispozici na [GitHubu](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Příklad topologie, která používá tuto komponentu a pracuje s Storm na HDInsight 3.5, najdete v [tématu GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Vytvoření topologie jazyka C#

Vytvoření projektu topologie Jazyka C# v sadě Visual Studio:

1. Otevřete sadu Visual Studio.

1. V okně **Start** vyberte **Vytvořit nový projekt**.

1. V okně **Vytvořit nový projekt** přejděte na **položku Storm Application**a vyberte ji **Další**.

1. V okně **Konfigurovat nový projekt** zadejte název **projektu** *WordCount*, přejděte nebo vytvořte cestu k adresáři **Umístění** pro projekt a pak vyberte **Vytvořit**.

    ![Storm aplikace, Konfigurace nového dialogového okna projektu, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Po vytvoření projektu byste měli mít následující soubory:

* *Program.cs*: Definice topologie pro váš projekt. Ve výchozím nastavení je vytvořena výchozí topologie, která se skládá z jednoho výtoku a jednoho šroubu.

* *Spout.cs*: Příklad výtoku, který vydává náhodná čísla.

* *Bolt.cs*: Ukázkový šroub, který udržuje počet čísel vyzařovaných výtokem.

Při vytváření projektu, NuGet stáhne nejnovější [SCP.NET balíček](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Provádění výtoku

Dále přidejte kód pro výtok, který se používá ke čtení dat v topologii z externího zdroje. Tato výtok náhodně vyzařuje větu do topologie.

1. Otevřít *Spout.cs*. Hlavními složkami výtoku jsou:

   * `NextTuple`: Volána Storm, když výtok je dovoleno vyzařovat nové řazené kolekce členů.

   * `Ack`(pouze transakční topologie): Zpracovává potvrzení spuštěná jinými součástmi v topologii pro řazené kolekce členů odeslané z výtoku. Potvrzení řazené kolekce členů umožní výtoku vědět, že byl úspěšně zpracován podřízené součásti.

   * `Fail`(pouze transakční topologie): Zpracovává řazené kolekce členů, které jsou zpracování selhání další součásti v topologii. Implementace `Fail` metody umožňuje znovu vyzařovat řazenou kolekce členů, aby mohla být znovu zpracována.

2. Nahraďte obsah `Spout` třídy následujícím textem:

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

### <a name="implement-the-bolts"></a>Realizovat šrouby

Nyní vytvořte dva šrouby Storm v tomto příkladu:

1. Odstraňte existující *soubor Bolt.cs* z projektu.

2. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **novou položku**. V seznamu vyberte **Storm Bolt**a zadejte *Splitter.cs* jako název. V kódu nového souboru změňte název `WordCount`oboru názvů na . Poté tento postup opakujte a vytvořte druhý šroub s názvem *Counter.cs*.

   * *Splitter.cs*: Implementuje šroub, který rozdělí věty na jednotlivá slova a vyzařuje nový proud slov.

   * *Counter.cs*: Implementuje šroub, který počítá každé slovo, a vydává nový proud slov a počet pro každé slovo.

     > [!NOTE]  
     > Tyto šrouby číst a zapisovat do datových proudů, ale můžete také použít šroub pro komunikaci se zdroji, jako je například databáze nebo služby.

3. Otevřít *Splitter.cs*. Má ve výchozím nastavení `Execute`pouze jednu metodu: . Metoda `Execute` je volána při šroubobdrží řazenou kolekce členů pro zpracování. Zde můžete číst a zpracovávat příchozí řazené kolekce členů a vyzařovat odchozí řazené kolekce členů.

4. Nahraďte obsah `Splitter` třídy následujícím kódem:

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

Výtoky a šrouby jsou uspořádány v grafu, který definuje, jak data toky mezi součástmi. Pro tuto topologii je graf následující:

![Schéma uspořádání výtoku a šroubových součástí, topologie bouře](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Výtok vyzařuje věty, které jsou distribuovány do instancí šroubu splitteru. Šroub rozdělovače přeruší věty na slova, která jsou distribuována do šroubu čítače.

Vzhledem k tomu, že instance Čítače obsahuje počet slov místně, chcete se ujistit, že určitá slova toku do stejné instance counter šroub. Každá instance sleduje konkrétní slova. Vzhledem k tomu, že šroub Splitter udržuje žádný stav, opravdu nezáleží na tom, která instance rozdělovače obdrží který trest.

Otevřít *Program.cs*. Důležitou metodou `GetTopologyBuilder`je , která se používá k definování topologie, která je odeslána storm. Nahraďte `GetTopologyBuilder` obsah následujícího kódu k implementaci topologie popsané výše:

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

1. Přejděte do **aplikace View** > **Server Explorer**.

1. Klikněte pravým tlačítkem na **Azure**, vyberte **Připojit k předplatnému Microsoft Azure...** a dokončete proces přihlášení.

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na projekt a **v HDInsightu**zvolte Odeslat bouři .

1. V dialogovém okně **Odeslat topologii** vyberte v rozevíracím seznamu **Storm Cluster** bouři v clusteru HDInsight a pak vyberte **Odeslat**. Zobrazením podokna **Výstup** můžete zkontrolovat, zda je odeslání úspěšné.

    Po úspěšném odeslání topologie by se mělo zobrazit okno **Zobrazení totopologie bouře** pro cluster. Chcete-li zobrazit informace o spuštěné topologii, zvolte topologii **WordCount** ze seznamu.

    ![Okno zobrazení topologie bouře, cluster HDInsight, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > **Topologií bouře** můžete také zobrazit z **Průzkumníka serveru**. Rozbalte **Azure** > **HDInsight**, klikněte pravým tlačítkem myši na storm v clusteru HDInsight a pak zvolte **Zobrazit topologii bouře**.

    Chcete-li zobrazit informace o součástech v topologii, vyberte komponentu v diagramu.

1. V části **Souhrn topologie** vyberte **Zabít,** chcete-li topologii zastavit.

    > [!NOTE]  
    > Topologií bouře se nadále spouští, dokud nejsou deaktivovány nebo dokud není cluster odstraněn.

## <a name="transactional-topology"></a>Transakční topologie

Předchozí topologie je netransakční. Součásti v topologii neimplementují funkce pro přehrání zpráv. Příklad transakční topologie vytvořte projekt a jako typ projektu vyberte **ukázku bouře.**

Transakční topologie implementují následující pro podporu přehrání dat:

* **Ukládání metadat do mezipaměti**: Výtok musí ukládat metadata o emitovaných datech, aby je bylo možné data znovu načíst a vyzařovat, pokud dojde k chybě. Vzhledem k tomu, že data vyzařovaná ukázkou jsou malá, nezpracovaná data pro každou řazenou n-tice jsou uložena ve slovníku pro přehrání.

* **Ack**: Každý šroub v `this.ctx.Ack(tuple)` topologii může volat potvrdit, že úspěšně zpracoval n-tice. Když všechny šrouby potvrdí n-tice, `Ack` je vyvolána metoda výtoku. Metoda `Ack` umožňuje výtoku odebrat data, která byla uložena do mezipaměti pro přehrání.

* **Selhání**: Každý `this.ctx.Fail(tuple)` šroub může volat k označení, že zpracování se nezdařilo pro řazenou kolekce členů. Selhání se šíří na `Fail` metodu výtoku, kde lze n-tice přehrát pomocí metadat uložených v mezipaměti.

* **ID sekvence**: Při vyzařování n-tice lze zadat jedinečné ID sekvence. Tato hodnota identifikuje řazenou`Ack` n-tice pro opětovné přehrání ( a `Fail`) zpracování. Například výtok v projektu **Ukázka bouře** používá následující volání metody při vyzařování dat:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Tento kód vysílá řazenou skupinu členů, která obsahuje větu do `lastSeqId`výchozího datového proudu, s hodnotou ID sekvence obsaženou v . V tomto `lastSeqId` příkladu se zintáží pro každou n-tice emitované.

Jak je ukázáno v projektu **Ukázka bouře,** zda je komponenta transakční lze nastavit za běhu, na základě konfigurace.

## <a name="hybrid-topology-with-c-and-java"></a>Hybridní topologie s C# a Javou

Pomocí nástrojů Data Lake pro Visual Studio můžete také vytvořit hybridní topologie, kde některé součásti jsou C# a jiné jsou Java.

Příklad hybridní topologie vytvořte projekt a vyberte **storm hybridní vzorek**. Tento typ vzorku ukazuje následující koncepty:

* **Java hubice** a **šroub C#:** Definováno ve `HybridTopology_javaSpout_csharpBolt` třídě.

  Transakční verze je definována `HybridTopologyTx_javaSpout_csharpBolt` ve třídě.

* **Výtok C#** a **Java** `HybridTopology_csharpSpout_javaBolt` šroub : Definováno ve třídě.

  Transakční verze je definována `HybridTopologyTx_csharpSpout_javaBolt` ve třídě.

  > [!NOTE]  
  > Tato verze také ukazuje, jak používat Clojure kód z textového souboru jako součást Java.

Chcete-li přepnout topologii, která se používá `[Active(true)]` při odeslání projektu, přesuňte příkaz do topologie, kterou chcete použít, před jeho odesláním do clusteru.

> [!NOTE]  
> Všechny soubory Java, které jsou požadovány jsou k dispozici jako součást tohoto projektu ve složce *JavaDependency.*

Při vytváření a odesílání hybridní topologie zvažte následující:

* Slouží `JavaComponentConstructor` k vytvoření instance třídy Java pro výtok nebo šroub.

* Slouží `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` k serializaci dat do nebo z komponent Java z objektů Java do JSON.

* Při odesílání topologie na server je nutné použít možnost **Další konfigurace** k určení **cest souboru jazyka Java**. Zadaná cesta by měla být adresář, který obsahuje jar soubory obsahující vaše java třídy.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET verze 0.9.4.203 zavádí novou třídu a metodu speciálně pro práci s výtokem Centra událostí (java hub, který čte z Event Hubs). Když vytvoříte topologii, která používá hub událostí hub (například pomocí ukázkové šablony **Storm EventHub Reader),** použijte následující api:

* `EventHubSpoutConfig`třída: Vytvoří objekt, který obsahuje konfiguraci komponenty výtoku.

* `TopologyBuilder.SetEventHubSpout`metoda: Přidá komponentu hubu událostí do topologie.

> [!NOTE]  
> Stále je nutné `CustomizedInteropJSONSerializer` použít serializovat data produkovaná výtokem.

## <a name="use-configurationmanager"></a>Použít nástroj ConfigurationManager

Nepoužívejte **ConfigurationManager** k načtení hodnot konfigurace z komponent šroubu a hubice. To může způsobit výjimku ukazatele null. Místo toho předejte konfiguraci projektu do topologie Storm jako dvojice klíčů a hodnot v kontextu topologie. Každá součást, která závisí na hodnotách konfigurace, je musí načíst z kontextu během inicializace.

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

Pokud použijete `Get` metodu k vrácení instance komponenty, musíte zajistit, že předá konstruktoru `Context` parametry a `Dictionary<string, Object>` parametry. Následující příklad je `Get` základní metoda, která správně předá tyto hodnoty:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Jak aktualizovat SCP.NET

Poslední verze SCP.NET upgrade balíčku podpory prostřednictvím NuGet. Pokud je k dispozici nová aktualizace, obdržíte oznámení o upgradu. Chcete-li upgrade zkontrolovat ručně, postupujte takto:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.

2. Ve správci balíčků vyberte **aktualizace**. Pokud je k dispozici aktualizace balíčku podpory SCP.NET, je uvedena. Vyberte **Aktualizovat** balíček a pak v dialogovém okně **Náhled změn** vyberte **OK,** abyste ho nainstalovali.

> [!IMPORTANT]  
> Pokud byl váš projekt vytvořen s dřívější verzí SCP.NET, která nepoužívala NuGet, je nutné provést následující kroky k aktualizaci na novější verzi:
>
> 1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.
> 2. Pomocí pole **Hledat** vyhledejte a přidejte `Microsoft.SCP.Net.SDK` do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Řešení běžných problémů s topologiemi

### <a name="null-pointer-exceptions"></a>Výjimky ukazatele Null

Pokud používáte topologii Jazyka C# s clusterem HDInsight založeným na Linuxu, komponenty šroubů a výtoků, které používají **ConfigurationManager** ke čtení nastavení konfigurace za běhu, mohou vrátit výjimky ukazatele null.

Konfigurace pro váš projekt je předána do topologie Storm jako dvojice klíčů a hodnot v kontextu topologie. Lze jej načíst z objektu slovníku, který je předán vašim součástem při jejich inicializování.

Další informace naleznete v části [Use ConfigurationManager](#use-configurationmanager) tohoto dokumentu.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Pokud používáte topologii Jazyka C# s clusterem HDInsight založeným na Linuxu, můžete narazit na následující chybu:

`System.TypeLoadException: Failure has occurred while loading a type.`

K této chybě dochází při použití binárního souboru, který není kompatibilní s verzí rozhraní .NET, kterou mono podporuje.

U clusterů HDInsight založených na Linuxu se ujistěte, že váš projekt používá binární soubory zkompilované pro rozhraní .NET 4.5.

### <a name="test-a-topology-locally"></a>Testování topologie místně

I když je snadné nasadit topologii do clusteru, v některých případech může být nutné otestovat topologii místně. Pomocí následujících kroků spusťte a otestujte ukázkovou topologii v tomto článku místně ve vývojovém prostředí.

> [!WARNING]  
> Místní testování funguje pouze pro základní topologie pouze c#. Nelze použít místní testování pro hybridní topologie nebo topologie, které používají více datových proudů.

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **příkaz Vlastnosti**. Ve vlastnostech projektu. Potom změňte **typ výstupu** na **konzolovou aplikaci**.

   ![APLIKACE HDInsight Storm, vlastnosti projektu, typ výstupu](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Nezapomeňte před nasazením topologie do clusteru změnit **typ výstupu** zpět do **knihovny tříd.**

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na projekt a potom vyberte **Přidat** > **novou položku**. Vyberte **Třída**a zadejte *LocalTest.cs* jako název třídy. Nakonec vyberte **přidat**.

1. Otevřete *LocalTest.cs*a `using` nahoře přidejte následující příkaz:

    ```csharp
    using Microsoft.SCP;
    ```

1. Jako obsah `LocalTest` třídy použijte následující kód:

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

    Udělejte si chvilku a přečtěte si komentáře kódu. Tento kód `LocalContext` používá ke spuštění součásti ve vývojovém prostředí. Zachová datový proud mezi součástmi do textových souborů na místní jednotce.

1. Otevřete *Program.cs*a přidejte `Main` do metody následující kód:

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

1. Uložte změny a pak vyberte **F5** nebo zvolte **Ladění** > **Start Ladění** zahájit projekt. Okno konzoly by se mělo zobrazit a stav protokolu jako průběh testů. Po `Tests finished` zjevu vyberte libovolnou klávesu pro zavření okna.

1. Pomocí **Průzkumníka Windows** vyhledejte adresář, který obsahuje váš projekt. (Například: *\\C: Uživatelé\\\\\\\\\<your_user_name>\\zdroj repos WordCount WordCount*.) Potom v tomto adresáři otevřete *bin*a vyberte *možnost Ladění*. Měli byste vidět textové soubory, které byly vytvořeny při spuštění testů: *sentences.txt*, *counter.txt*a *splitter.txt*. Otevřete každý textový soubor a zkontrolujte data.

   > [!NOTE]  
   > Řetězcová data zůstanou v těchto souborech jako pole desetinných hodnot. Například `[[97,103,111]]` v souboru **splitter.txt** představuje slovo *před*.

> [!NOTE]  
> Nezapomeňte nastavit **typ projektu** zpět do **knihovny tříd** ve vlastnostech projektu před nasazením do clusteru Storm na HDInsight.

### <a name="log-information"></a>Informace protokolu

Informace z komponent topologie můžete snadno `Context.Logger`protokolovat pomocí aplikace . Například následující příkaz vytvoří položku informačního protokolu:

`Context.Logger.Info("Component started");`

Protokolované informace lze zobrazit z **protokolu služeb Hadoop**, který se nachází v **Průzkumníku serveru**. Rozbalte položku pro svůj cluster Storm v clusteru HDInsight a potom rozbalte **protokol služeb Hadoop**. Nakonec vyberte soubor protokolu, který chcete zobrazit.

> [!NOTE]  
> Protokoly jsou uloženy v účtu úložiště Azure, který používá váš cluster. Chcete-li zobrazit protokoly v sadě Visual Studio, musíte se přihlásit k předplatnému Azure, které vlastní účet úložiště.

### <a name="view-error-information"></a>Zobrazit informace o chybě

Chcete-li zobrazit chyby, ke kterým došlo v běžící topologii, postupujte takto:

1. V **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na cluster Storm v clusteru HDInsight a vyberte **zobrazit topologii bouře**.

   Pro **výtok** a **šrouby**, sloupec **Poslední chyba** obsahuje informace o poslední chybě.

2. Vyberte **ID výtoku** nebo **ID šroubu** pro součást, která má uvedenou chybu. Stránka podrobností zobrazuje další informace o chybě v části **Chyby** v dolní části stránky.

3. Chcete-li získat další informace, vyberte **port** z oddílu **Executors** na stránce, chcete-li zobrazit protokol pracovníka bouře za posledních několik minut.

### <a name="errors-submitting-topologies"></a>Chyby při odesílání topologií

Pokud narazíte na chyby odesílání topologie do HDInsight, můžete najít protokoly pro součásti na straně serveru, které zpracovávají odesílání topologie v clusteru HDInsight. Chcete-li tyto protokoly stáhnout, použijte následující příkaz z příkazového řádku:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Nahraďte *sshuser* uživatelským účtem SSH pro cluster. Nahraďte *název clusteru* názvem clusteru HDInsight. Další informace o `scp` `ssh` používání a používání HDInsight najdete v [tématu Použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Odeslání může selhat z několika důvodů:

* Sada JDK není nainstalována nebo není v cestě.
* Požadované závislosti jazyka Java nejsou zahrnuty v odeslání.
* Závislosti jsou nekompatibilní.
* Názvy topologie jsou duplikovány.

Pokud soubor protokolu *hdinsight-scpwebapi.out* obsahuje `FileNotFoundException`soubor protokolu , může být výjimka způsobena následujícími podmínkami:

* JDK není v cestě na vývojovéprostředí. Ověřte, zda je jdk nainstalován `%JAVA_HOME%/bin` ve vývojovém prostředí a že je v cestě.
* Chybí vám závislost na Javě. Ujistěte se, že jste včetně všech požadovaných .jar soubory jako součást podání.

## <a name="next-steps"></a>Další kroky

Příklad zpracování dat z centra událostí najdete v tématu [Zpracování událostí z Center událostí Azure s Stormem na HDInsightu](apache-storm-develop-csharp-event-hub-topology.md).

Příklad topologie Jazyka C#, který rozděluje data datového proudu na více datových proudů, naleznete v [příkladu C# Storm](https://github.com/Blackmist/csharp-storm-example).

Další informace o vytváření topologií jazyka C# najdete v tématu [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Další způsoby práce s HDInsight a další Storm na ukázkách HDInsight, najdete v následujících dokumentech:

**Microsoft SCP.NET**

* [Programovací průvodce SCP pro Apache Storm v Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm v HDInsight**

* [Nasazení a správa topologie Apache Storm na Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Příklad topologie Apache Storm v Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop na HDInsight**

* [Co je Apache Hive a HiveQL na Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Použití MapReduce v Apache Hadoopu ve službě HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase ve službě HDInsight**

* [Použití Apache HBase v Azure HDInsightu](../hbase/apache-hbase-tutorial-get-started-linux.md)
