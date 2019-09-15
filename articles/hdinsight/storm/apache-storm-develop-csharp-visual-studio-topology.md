---
title: Apache Storm topologie se sadou Visual Studio C# a – Azure HDInsight
description: Naučte se vytvářet topologie zaplavení v C#. Pomocí nástrojů Hadoop pro Visual Studio vytvořte v aplikaci Visual Studio jednoduchou topologii počtu slov.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 828ec2b925535df3f925093466556447e703cd76
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003810"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Vývoj C# topologií pro Apache Storm pomocí nástrojů Data Lake pro Visual Studio

Naučte se vytvářet C# Apache Storm topologii pomocí nástrojů Azure Data Lake (Apache Hadoop) pro Visual Studio. Tento dokument vás provede procesem vytvoření projektu zaplavení v aplikaci Visual Studio, jeho otestováním lokálně a nasazením do Apache Storm v clusteru Azure HDInsight.

Naučíte se také, jak vytvořit hybridní topologie, C# které používají komponenty a součásti Java.

> [!NOTE]  
> I když postup v tomto dokumentu spoléhá na vývojové prostředí Windows se sadou Visual Studio, zkompilovaný projekt lze odeslat do clusteru HDInsight se systémem Linux nebo Windows. Pouze clustery se systémem Linux vytvořené po 28. říjnu 2016 podporují topologie SCP.NET.

Pokud chcete použít C# topologii s clusterem se systémem Linux, musíte aktualizovat balíček NuGet Microsoft. SCP. NET. SDK, který používá váš projekt, na verzi 0.10.0.6 nebo novější. Verze balíčku se zároveň musí shodovat s hlavní verzí Stormu nainstalovanou ve službě HDInsight.

| Verze HDInsight | Verze Apache Storm | Verze SCP.NET | Výchozí verze mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(pouze v HDInsight se systémem Windows) | Není k dispozici |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> Topologie jazyka C# v clusterech založených na Linuxu musí používat technologii .NET 4.5. a pro spuštění v clusteru HDInsight musí používat Mono. Ověřte [kompatibilitu mono](https://www.mono-project.com/docs/about-mono/compatibility/) pro potenciální nekompatibility.

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

Můžete vyvíjet C# topologie pomocí SCP.NET pomocí jedné z následujících verzí sady Visual Studio:

* Visual Studio 2012 s aktualizací Update 4

* Visual Studio 2013 s aktualizací Update 4 nebo [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Komunita sady Visual Studio 2015 nebo [Visual studio 2015](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (jakákoli edice)

## <a name="install-data-lake-tools-for-visual-studio"></a>Nainstalovat Data Lake nástroje pro Visual Studio

Chcete-li nainstalovat nástroje Data Lake Tools for Visual Studio, postupujte podle kroků v části [Začínáme používat data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Nainstalovat Java

Při odeslání topologie negenerovaného toku ze sady Visual Studio vygeneruje SCP.NET soubor zip, který obsahuje topologii a závislosti. Jazyk Java slouží k vytvoření těchto souborů zip, protože používá formát, který je více kompatibilní s clustery se systémem Linux.

1. Do vývojového prostředí nainstalujte sadu Java Developer Kit (JDK) 7 nebo novější. Oracle JDK můžete získat od [Oracle](https://aka.ms/azure-jdks). Můžete také použít [jiné distribuce Java](https://openjdk.java.net/).

2. Proměnná `JAVA_HOME` prostředí musí odkazovat na adresář, který obsahuje Java.

3. Proměnná prostředí musí `%JAVA_HOME%\bin`zahrnovatadresář. `PATH`

K ověření, že jsou C# správně nainstalované jazyky Java a JDK, můžete použít tuto konzolovou aplikaci:

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
> Ne všechny šablony pracují se systémem Linux HDInsight. Balíčky NuGet používané šablonami nemusí být kompatibilní s mono. Ověřte v dokumentu o [kompatibilitě mono](https://www.mono-project.com/docs/about-mono/compatibility/) a použijte [analyzátor přenositelnosti .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) k identifikaci potenciálních problémů.

V krocích v tomto dokumentu použijete k vytvoření topologie typ projektu základní aplikace pro zaplavení.

### <a name="apache-hbase-templates-notes"></a>Poznámky k šablonám Apache HBA

Šablony pro čtečku a zapisovače HBA používají adaptéry HBA REST API, ne adaptéry Java API pro komunikaci s HBA v clusteru HDInsight.

### <a name="eventhub-templates-notes"></a>Poznámky k šablonám EventHub

> [!IMPORTANT]  
> Komponenta EventHub Spout na bázi Java, která je součástí šablony čtečky EventHub, nemusí fungovat se zaplavou ve službě HDInsight verze 3,5 nebo novější. Aktualizovaná verze této součásti je k dispozici na [GitHubu](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Příklad topologie, která používá tuto součást a pracuje se zachováním v HDInsight 3,5, najdete v tématu [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Vytvoření C# topologie

1. Otevřete Visual Studio, vyberte **soubor** > **Nový**a pak vyberte **projekt**.

2. V okně **Nový projekt** rozbalte položku **nainstalované** > **šablony**a vyberte možnost **Azure Data Lake**. V seznamu šablon vyberte možnost zaplavit **aplikaci**. V dolní části obrazovky jako název aplikace zadejte **WORDCOUNT** .

    ![Snímek obrazovky okna s novým projektem](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

3. Po vytvoření projektu byste měli mít následující soubory:

   * **Program.cs**: Tento soubor definuje topologii pro váš projekt. Ve výchozím nastavení se vytvoří výchozí topologie, která se skládá z jednoho Spout a jednoho šroubu.

   * **Spout.cs**: Příklad Spout, který generuje náhodná čísla.

   * **Bolt.cs**: Příklad, který udržuje počet čísel emitovaných Spout.

     Při vytváření projektu NuGet stáhne nejnovější [balíček SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementace rozhraní Spout

1. Otevřete **Spout.cs**. Spoutů se používají ke čtení dat z topologie z externího zdroje. Hlavní součásti pro Spout jsou:

   * **NextTuple**: Volá se se zavoláním, když je povolený Spout k vygenerování nových řazených kolekcí členů.

   * **Potvrzení** (pouze transakční topologie): Zpracovává potvrzení iniciovaná jinými součástmi v topologii pro řazené kolekce členů odeslané z Spout. Potvrzením řazené kolekce členů umožní Spout zjistit, že byl úspěšně zpracován pomocí součástí podřízené součásti.

   * **Selhání** (pouze transakční topologie): Zpracovává řazené kolekce členů, které zpracovávají selhání jiných komponent v topologii. Implementace metody selhání umožňuje znovu vygenerovat řazenou kolekci členů, aby ji bylo možné znovu zpracovat.

2. Nahraďte obsah třídy **Spout** následujícím textem: Tento Spout náhodně generuje větu do topologie.

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

1. Odstraňte existující soubor **Bolt.cs** z projektu.

2. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte možnost **Přidat** > **novou položku**. V seznamu vyberte možnost **přešroubování**a jako název zadejte **Splitter.cs** . Zopakováním tohoto postupu vytvořte druhý šroub s názvem **Counter.cs**.

   * **Splitter.cs**: Implementuje šroub, který rozdělí věty na jednotlivá slova a emituje nový proud slov.

   * **Counter.cs**: Implementuje šroub, který počítá každé slovo, a vygeneruje nový proud slov a počet pro každé slovo.

     > [!NOTE]  
     > Tyto šrouby čtou a zapisují do datových proudů, ale můžete také použít šroub ke komunikaci se zdroji, jako je databáze nebo služba.

3. Otevřete **Splitter.cs**. Ve výchozím nastavení má pouze jednu metodu: **Provést**. Metoda Execute je volána, když šroub obdrží řazenou kolekci členů ke zpracování. Tady můžete číst a zpracovávat příchozí řazené kolekce členů a generovat odchozí řazené kolekce členů.

4. Obsah třídy **rozdělovače** nahraďte následujícím kódem:

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

5. Otevřete **Counter.cs**a nahraďte obsah třídy následujícím kódem:

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

![Diagram uspořádání komponent](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Věty jsou vydávány z Spout a jsou distribuovány do instancí rozdělovače. Šroubce přerušuje věty na slova, která jsou distribuována do čítače čítačů.

Vzhledem k tomu, že počet slov se v instanci čítače nachází místně, chcete zajistit, aby určitá slova probíhala v toku ke stejné instanci počítadla. Každá instance uchovává záznam o konkrétních slovech. Vzhledem k tomu, že rozdělovač oddělovače udržuje žádný stav, nezáleží na tom, která instance rozdělovače obdrží tuto větu.

Otevřete **program.cs**. Důležitou metodou je **GetTopologyBuilder**, která slouží k definování topologie, která je odeslána do přeplavení. Nahraďte obsah **GetTopologyBuilder** následujícím kódem k implementaci topologie popsané dříve:

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

## <a name="submit-the-topology"></a>Odeslat topologii

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Odeslat pro**zaplavení v HDInsight.

   > [!NOTE]  
   > Pokud se zobrazí výzva, zadejte přihlašovací údaje k vašemu předplatnému Azure. Pokud máte více než jedno předplatné, přihlaste se k počítači, který obsahuje vaše navýšení na clusteru HDInsight.

2. Z rozevíracího seznamu **cluster** pro zaplavení vyberte svou navýšení na clusteru HDInsight a pak vyberte **Odeslat**. Můžete sledovat, zda je odeslání úspěšné, pomocí okna **výstup** .

3. Po úspěšném odeslání topologie by se měly zobrazit **topologie** pro zaplavení clusteru. Výběrem topologie **WORDCOUNT** ze seznamu zobrazíte informace o spuštěné topologii.

   > [!NOTE]  
   > **Topologie** **přePrůzkumník serveru**můžete zobrazit také z. Rozbalte **Azure** > **HDInsight**, klikněte pravým tlačítkem na cluster HDInsight a pak vyberte **Zobrazit topologie**přeplavování.

    Chcete-li zobrazit informace o součástech v topologii, dvakrát klikněte na součást v diagramu.

4. V zobrazení **Souhrn topologie** kliknutím na tlačítko **Kill** zastavte topologii.

   > [!NOTE]  
   > Topologie zaplavování se nadále spouštějí, dokud nejsou deaktivovány, nebo když se cluster odstraní.

## <a name="transactional-topology"></a>Transakční topologie

Předchozí topologie je jiná než transakční. Komponenty v topologii neimplementují funkce pro přehrávání zpráv. Příklad transakční topologie získáte tak, že vytvoříte projekt a jako typ projektu vyberete možnost Nevytvářet **vzorek** .

Transakční topologie implementují následující, aby podporovaly přehrávání dat:

* **Ukládání metadat do mezipaměti**: Spout musí ukládat metadata o vysílaných datech, aby se data mohla načíst a emitovat znovu, pokud dojde k selhání. Vzhledem k tomu, že data vygenerovaná ukázkou jsou malá, jsou nezpracovaná data pro každou řazenou kolekci členů uložena ve slovníku pro opětovné přehrání.

* **Potvrzení**: Každé pole topologie může volat `this.ctx.Ack(tuple)` , aby bylo možné potvrdit, že úspěšně zpracovalo řazenou kolekci členů. Když všechny šrouby potvrdí řazenou kolekci členů, `Ack` vyvolá se metoda Spout. `Ack` Metoda umožňuje Spout odebrat data, která byla uložena do mezipaměti pro opětovné přehrání.

* **Selhání**: Každé šroubové volání `this.ctx.Fail(tuple)` může značit, že zpracování pro řazenou kolekci členů selhalo. Selhání se šíří do `Fail` metody Spout, kde je možné znovu přehrát řazenou kolekci členů pomocí metadat uložených v mezipaměti.

* **ID sekvence**: Při generování řazené kolekce členů lze zadat jedinečné ID sekvence. Tato hodnota určuje počet řazené kolekce členů pro zpracování opětovného přehrání (ACK a selhání). Například Spout v projektu s **ukázkovým** zaplavení při vygenerování dat používá následující:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Tento kód vygeneruje řazenou kolekci členů, která obsahuje větu k výchozímu datovému proudu, s hodnotou ID sekvence obsaženou v **lastSeqId**. V tomto příkladu se **lastSeqId** zvyšuje pro každou vydanou řazenou kolekci členů.

Jak je znázorněno v projektu **ukázkového** navýšení, zda je součást transakční, lze nastavit za běhu na základě konfigurace.

## <a name="hybrid-topology-with-c-and-java"></a>Hybridní topologie pomocí C# a Java

Můžete také použít nástroje Data Lake Tools for Visual Studio k vytvoření hybridních topologií, kde některé součásti C# a jiné jsou Java.

Příklad hybridní topologie získáte tak, že vytvoříte projekt a vyberete možnost navýšit **hybridní vzorek**. Tento vzorový typ předvádí následující koncepty:

* **Java Spout** a  **C# šroub**: Definováno v **HybridTopology_javaSpout_csharpBolt**.

    * Transakční verze je definována v **HybridTopologyTx_javaSpout_csharpBolt**.

* Spout a **Java**: **C#** Definováno v **HybridTopology_csharpSpout_javaBolt**.

    * Transakční verze je definována v **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]  
  > Tato verze také ukazuje, jak použít kód Clojure z textového souboru jako součást Java.

Chcete-li přepnout topologii, která se používá při odeslání projektu, přesuňte `[Active(true)]` příkaz do topologie, kterou chcete použít, před odesláním do clusteru.

> [!NOTE]  
> Všechny soubory Java, které jsou požadovány, jsou k dispozici jako součást tohoto projektu ve složce **JavaDependency** .

Při vytváření a odesílání hybridní topologie Vezměte v úvahu následující skutečnosti:

* Pomocí **JavaComponentConstructor** můžete vytvořit instanci třídy Java pro Spout nebo šroub.

* K serializaci dat do komponent Java nebo z nich z objektů Java do formátu JSON použijte **Microsoft. SCP. CustomizedInteropJSONSerializer. getlang.** .

* Při odesílání topologie na server je nutné použít možnost **dalších konfigurací** k určení **cest k souborům Java**. Zadaná cesta by měla být adresář, který obsahuje soubory JAR, které obsahují vaše třídy Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET verze 0.9.4.203 zavádí novou třídu a metodu specificky pro práci s centrem událostí Spout (Java Spout, který čte z Event Hubs). Při vytváření topologie, která používá Spout centra událostí, použijte následující metody:

* Třída **EventHubSpoutConfig** : Vytvoří objekt, který obsahuje konfiguraci pro komponentu Spout.

* **TopologyBuilder. SetEventHubSpout** – metoda: Přidá do topologie komponentu Spout centra událostí.

> [!NOTE]  
> K serializaci dat vytvářených rozhraním Spout je však stále nutné použít rozhraní **CustomizedInteropJSONSerializer** .

## <a id="configurationmanager"></a>Použití ConfigurationManager

Nepoužívejte **ConfigurationManager** k načtení hodnot konfigurace ze součástí šroubů a Spout. V takovém případě může dojít k výjimce ukazatele s hodnotou null. Místo toho je konfigurace pro váš projekt předána do topologie přetvoření jako dvojice klíč-hodnota v kontextu topologie. Každá komponenta, která závisí na hodnotách konfigurace, musí být při inicializaci načítána z kontextu.

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
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Použijete `Get` -li metodu pro vrácení instance komponenty, je nutné zajistit, aby do konstruktoru předala `Context` parametry a `Dictionary<string, Object>` . Následující příklad je základní `Get` metoda, která správně předává tyto hodnoty:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Postup aktualizace SCP.NET

Nejnovější verze SCP.NET podporují upgrade balíčku prostřednictvím NuGet. Když je k dispozici nová aktualizace, obdržíte oznámení o upgradu. Chcete-li provést ruční kontrolu upgradu, postupujte podle následujících kroků:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.

2. Ve Správci balíčků vyberte **aktualizace**. Pokud je aktualizace k dispozici, je uvedena v seznamu. Kliknutím na **aktualizovat** balíček ho nainstalujete.

> [!IMPORTANT]  
> Pokud byl projekt vytvořen pomocí starší verze SCP.NET, která nepoužívala NuGet, je nutné provést následující kroky, aby se aktualizovala na novější verzi:
>
> 1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.
> 2. Pomocí **vyhledávacího** pole vyhledejte a potom do projektu přidejte **Microsoft. SCP. NET. SDK** .

## <a name="troubleshoot-common-issues-with-topologies"></a>Řešení běžných potíží s topologiemi

### <a name="null-pointer-exceptions"></a>Výjimky ukazatele s hodnotou null

Pokud používáte C# topologii s clusterem HDInsight se systémem Linux, musí komponenty šroubů a Spout, které používají **ConfigurationManager** ke čtení konfiguračních nastavení za běhu, vracet výjimky ukazatele s hodnotou null.

Konfigurace pro váš projekt je předána do topologie přetvoření jako dvojice klíč a hodnota v kontextu topologie. Může být načten z objektu Dictionary, který je předán komponentám při inicializaci.

Další informace najdete v části [ConfigurationManager](#configurationmanager) tohoto dokumentu.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Pokud používáte C# topologii s clusterem HDInsight se systémem Linux, může dojít k následující chybě:

    System.TypeLoadException: Failure has occurred while loading a type.

K této chybě dochází, pokud použijete binární soubor, který není kompatibilní s verzí rozhraní .NET, kterou mono podporuje.

U clusterů HDInsight se systémem Linux se ujistěte, že váš projekt používá binární soubory kompilované pro .NET 4,5.

### <a name="test-a-topology-locally"></a>Místní otestování topologie

I když je možné topologii nasadit do clusteru, může být v některých případech nutné místně testovat topologii. Následující postup použijte k místnímu spuštění a otestování ukázkové topologie v tomto článku ve vývojovém prostředí.

> [!WARNING]  
> Místní testování funguje pouze pro základní C#topologie. Místní testování nelze použít pro hybridní topologie nebo topologie, které používají více datových proudů.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **vlastnosti**. Ve vlastnostech projektu změňte **Typ výstupu** na **konzolovou aplikaci**.

    ![Snímek obrazovky s vlastnostmi projektu se zvýrazněným typem výstupu](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Nezapomeňte změnit **Typ výstupu** zpátky do **knihovny tříd** před nasazením topologie do clusteru.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte možnost **Přidat** > **novou položku**. Vyberte **Třída**a jako název třídy zadejte **LocalTest.cs** . Nakonec klikněte na tlačítko **Přidat**.

1. Otevřete **LocalTest.cs**a přidejte následující příkaz **using** v horní části:

    ```csharp
    using Microsoft.SCP;
    ```

1. Jako obsah třídy **LocalTest** použijte následující kód:

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

    Věnujte si chvilku, abyste si přečetli komentáře ke kódu. Tento kód používá **LocalContext** ke spuštění komponent ve vývojovém prostředí a ukládá datový proud mezi komponentami do textových souborů na místním disku.

1. Otevřete **program.cs**a do metody **Main** přidejte následující:

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

1. Uložte změny a pak klikněte na **F5** nebo vyberte **ladit** > **Spustit ladění** a spusťte tak projekt. Mělo by se zobrazit okno konzoly a log status jako průběh testů. Po zobrazení **dokončených testů** stiskněte libovolnou klávesu pro zavření okna.

1. Pomocí **Průzkumníka Windows** vyhledejte adresář, který obsahuje váš projekt. Příklad: **C:\Users\<your_user_name > \Documents\Visual Studio 2013 \ Projects\WordCount\WordCount**. V tomto adresáři otevřete **přihrádku**a klikněte na tlačítko **ladit**. Měli byste vidět textové soubory, které byly vytvořeny při spuštění testů: věty. txt, Counter. txt a rozdělovač. txt. Otevřete každý textový soubor a zkontrolujte data.

   > [!NOTE]  
   > Řetězcová data v těchto souborech přetrvají jako pole desítkových hodnot. Například \[[97 103 111]] v souboru **rozdělovače. txt** je slovo *a*.

> [!NOTE]  
> Před nasazením do clusteru HDInsight nezapomeňte nastavit **typ projektu** zpátky na **knihovnu tříd** .

### <a name="log-information"></a>Informace protokolu

Informace ze svých komponent topologie můžete snadno protokolovat pomocí `Context.Logger`. Například následující příkaz vytvoří informační položku protokolu:

```csharp
Context.Logger.Info("Component started");
```

Protokolované informace můžete zobrazit z **protokolu služby Hadoop**, který najdete v **Průzkumník serveru**. Rozbalte položku pro vaše vyplavení v clusteru HDInsight a potom rozbalte **protokol služby Hadoop**. Nakonec vyberte soubor protokolu, který chcete zobrazit.

> [!NOTE]  
> Protokoly se ukládají do účtu služby Azure Storage, který používá váš cluster. Chcete-li zobrazit protokoly v aplikaci Visual Studio, musíte se přihlásit k předplatnému Azure, které vlastní účet úložiště.

### <a name="view-error-information"></a>Zobrazit informace o chybě

Chcete-li zobrazit chyby, ke kterým došlo ve spuštěné topologii, použijte následující postup:

1. V **Průzkumník serveru**klikněte pravým tlačítkem na cluster se systémem HDInsight a vyberte **Zobrazit topologie**přeplavení.

2. Sloupec **Poslední chyba** pro **Spout** a **šrouby**obsahuje informace o Poslední chybě.

3. Vyberte **ID Spout** nebo **ID šroubu** pro komponentu, u které je uvedená chyba. Na zobrazené stránce podrobností se v části **chyby** v dolní části stránky zobrazí další informace o chybě.

4. Pokud chcete získat další informace, vyberte **port** z části **vykonavatelé** na stránce, abyste viděli protokol pracovního procesu pro více než posledních pár minut.

### <a name="errors-submitting-topologies"></a>Chyby při odesílání topologií

Pokud narazíte na chyby při odesílání topologie do služby HDInsight, můžete najít protokoly pro serverové komponenty, které zpracovávají odesílání topologie v clusteru HDInsight. Chcete-li načíst tyto protokoly, použijte následující příkaz z příkazového řádku:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Nahraďte __sshuser__ uživatelským účtem SSH pro cluster. Položku __název_clusteru__ nahraďte názvem clusteru HDInsight. Další informace o používání `scp` a `ssh` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Odesílání může selhat z několika důvodů:

* JDK není nainstalován nebo není v cestě.
* Požadované závislosti Java nejsou zahrnuté do odeslání.
* Nekompatibilní závislosti
* Duplicitní názvy topologie.

`hdinsight-scpwebapi.out` Pokud protokol`FileNotFoundException`obsahuje, může to být způsobeno následujícími podmínkami:

* JDK není v cestě k vývojovému prostředí. Ověřte, že je JDK nainstalovaný ve vývojovém prostředí a že `%JAVA_HOME%/bin` je v cestě.
* Chybí závislost Java. Ujistěte se, že jako součást odeslání budete zahrnovat všechny požadované soubory. jar.

## <a name="next-steps"></a>Další postup

Příklad zpracování dat z Event Hubs najdete v tématu [zpracování událostí z Azure Event Hubs se](apache-storm-develop-csharp-event-hub-topology.md)zaplavou v HDInsight.

Příklad C# topologie, která rozdělí streamovaná data do více datových proudů, najdete v tématu [ C# příklad](https://github.com/Blackmist/csharp-storm-example)zaplavení.

Další informace o vytváření C# topologií najdete v tématu [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Další způsoby, jak pracovat se službou HDInsight a větším využitím ukázek HDInsight, najdete v následujících dokumentech:

**Microsoft SCP.NET**

* [Průvodce programováním SCP](apache-storm-scp-programming-guide.md)

**Apache Storm ve službě HDInsight**

* [Nasazení a monitorování topologií pomocí Apache Storm v HDInsight](apache-storm-deploy-monitor-topology.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)

**Apache Hadoop ve službě HDInsight**

* [Použití Apache Hive s Apache Hadoop v HDInsight](../hadoop/hdinsight-use-hive.md)
* [Použití systému Apache prasete s Apache Hadoop v HDInsight](../hadoop/hdinsight-use-pig.md)
* [Použití Apache Hadoop MapReduce s Apache Hadoop v HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBA v HDInsight**

* [Začínáme s Apache Hbas v HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
