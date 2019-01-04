---
title: Topologií Apache Storm pomocí sady Visual Studio a C# – Azure HDInsight
description: Zjistěte, jak vytváření topologií Storm v jazyce C#. Vytvoření topologii počtu slov v sadě Visual Studio pomocí nástrojů Hadoop pro Visual Studio.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: cf258637311cd22964723da6bad3451dff6cccf6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632005"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Vývoj topologií C# pro Apache Storm pomocí nástrojů Data Lake pro Visual Studio

Zjistěte, jak vytvořit C# topologií Apache Storm pomocí nástrojů Azure Data Lake (Apache Hadoopu) pro Visual Studio. Tento dokument vás provede procesem vytvoření projektu Storm v sadě Visual Studio, jejím místním otestováním a jeho nasazení do Apache Storm v clusteru Azure HDInsight.

Také se dozvíte, jak vytvořit hybridní topologie, které pomocí jazyka C# a komponent v jazyce Java.

> [!NOTE]  
> Přestože postup v tomto dokumentu využívají prostředí pro vývoj Windows pomocí sady Visual Studio, zkompilovaný projekt můžete odeslat do clusteru s Linuxem nebo HDInsight se systémem Windows. Clustery se systémem Linux vytvořené po 28. říjnem 2016, podporují pouze topologie SCP.NET.

Topologie C# pomocí clusteru se systémem Linux, musíte aktualizovat balíček NuGet Microsoft.scp.NET.SDK, který používá váš projekt na verzi 0.10.0.6 nebo vyšší. Verze balíčku se zároveň musí shodovat s hlavní verzí Stormu nainstalovanou ve službě HDInsight.

| Verze HDInsight | Apache Storm verze | Verze SCP.NET | Výchozí verze Mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(pouze v HDInsight se systémem Windows) | Není k dispozici |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> Topologie jazyka C# v clusterech založených na Linuxu musí používat technologii .NET 4.5. a pro spuštění v clusteru HDInsight musí používat Mono. Zkontrolujte [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) kde najdete potenciální nekompatibility.

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

Pomocí jedné z následujících verzí sady Visual Studio můžete vyvíjet topologie C# s SCP.NET:

* Visual Studio 2012 s aktualizací Update 4

* Visual Studio 2013 with Update 4 nebo [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 nebo [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (libovolná edice)

## <a name="install-data-lake-tools-for-visual-studio"></a>Nainstalovat Data Lake tools pro Visual Studio

Chcete-li nainstalovat nástroje Data Lake pro Visual Studio, postupujte podle kroků v [začněte používat nástroje Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Nainstalovat Java

Při odesílání topologie Storm ze sady Visual Studio SCP.NET generuje soubor zip, který obsahuje topologie a závislosti. Java se používá k vytvoření těchto souborů zip vzhledem k tomu používá formátu, který je kompatibilní více s clustery založené na Linuxu.

1. Nainstalujte Java Developer Kit (JDK) 7 nebo novějším na vašem vývojovém prostředí. Získáte sadu JDK Oracle od [Oracle](https://aka.ms/azure-jdks). Můžete také použít [jiné distribuce Java](https://openjdk.java.net/).

2. `JAVA_HOME` Proměnné prostředí musí odkazovat na adresář, který obsahuje Java.

3. `PATH` Musí obsahovat proměnné prostředí `%JAVA_HOME%\bin` adresáře.

Chcete-li ověřit, že jsou správně nainstalovány Java a sadu JDK, můžete použít následující aplikaci konzoly C#:

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

## <a name="apache-storm-templates"></a>Apache Storm šablony

Nástroje Data Lake pro Visual Studio poskytuje následující šablony:

| Typ projektu | Demonstruje |
| --- | --- |
| Aplikace Storm |Prázdný projekt topologie Storm. |
| Ukázka zapisovače Azure SQL Storm |Jak napsat do služby Azure SQL Database. |
| Ukázka Čtenář služby Azure Cosmos DB Storm |Jak číst ze služby Azure Cosmos DB. |
| Ukázka zapisovače služby Azure Cosmos DB Storm |Způsob zápisu do služby Azure Cosmos DB. |
| Ukázka čtečky centra událostí stormu |Jak číst ze služby Azure Event Hubs. |
| Ukázka zapisovače centra událostí stormu |Jak napsat do služby Azure Event Hubs. |
| Ukázka HBase čtečky Storm |Jak číst z HBase v clusterech HDInsight. |
| Ukázka HBase zapisovače Storm |Postup zápisu do HBase v HDInsight clusterů. |
| Ukázka hybridní Storm |Způsob použití komponent v jazyce Java. |
| Ukázka Storm |Topologii počtu slov základní. |

> [!WARNING]  
> Ne všechny šablony pracovat s Linuxovým systémem HDInsight. Balíčky NuGet používané šablony nemusí být kompatibilní s Mono. Zkontrolujte [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) Dokumentovat a použít [.NET Portability Analyzeru](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) identifikovat potenciální problémy.

V krocích v tomto dokumentu použijete k vytvoření topologie základní aplikace Storm typ projektu.

### <a name="apache-hbase-templates-notes"></a>Apache HBase šablony poznámky

Šablony čtečky a zapisovače HBase pomocí REST API HBase není Java API HBase, komunikovat s HBase v clusteru HDInsight.

### <a name="eventhub-templates-notes"></a>Poznámky k šablony centra událostí

> [!IMPORTANT]  
> Součásti spout založené na jazyce Java centra událostí je součástí šablony čtečky centra událostí nemusí fungovat se Stormem v HDInsight verze 3.5 nebo novější. Je k dispozici na aktualizovanou verzi této součásti [Githubu](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Topologii příkladu, který používá toto komponenty a spolupracuje se Stormem v HDInsight 3.5, naleznete v tématu [Githubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Vytvořit topologie C#

1. Otevřít Visual Studio, vyberte **souboru** > **nový**a pak vyberte **projektu**.

2. Z **nový projekt** okna rozbalte **nainstalováno** > **šablony**a vyberte **Azure Data Lake**. V seznamu šablon vyberte **aplikace Storm**. V dolní části obrazovky zadejte **WordCount** jako název aplikace.

    ![Okno snímek obrazovky nového projektu](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Po vytvoření projektu, byste měli mít následující soubory:

   * **Soubor program.cs**: Tento soubor definuje topologie pro váš projekt. Ve výchozím nastavení se vytvoří výchozí topologii, která se skládá z jednoho spout a bolt jeden.

   * **Spout.cs**: Spout příklad, který generuje náhodná čísla.

   * **Bolt.cs**: Příklad bolt, který udržuje počet čísel, protože ho vygeneroval spout.

     Při vytváření projektu NuGet stáhne nejnovější [SCP.NET balíčku](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementace spout

1. Otevřít **Spout.cs**. Spoutů slouží ke čtení z externího zdroje dat v topologii. Hlavní součásti spout jsou:

   * **NextTuple**: Voláno rozhraním Storm, když spout může generovat nové řazené kolekce členů.

   * **ACK** (pouze pro transakční topologie): Zpracovává potvrzení iniciované jinými součástmi v topologii pro odeslaný spout řazené kolekce členů. Potvrdil řazené kolekce členů umožňuje spout vědět, že byl úspěšně zpracován podřízené součásti.

   * **Selhání** (pouze pro transakční topologie): Obslužné rutiny řazených kolekcí členů, které jsou selhání zpracování jiné komponenty v topologii. Implementace metody selhání umožňuje znovu generovat řazené kolekce členů tak, že je možné je zpracovat znovu.

2. Nahraďte obsah **Spout** třídy s následujícím textem: Tato spout náhodně vysílá větu do topologie.

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

### <a name="implement-the-bolts"></a>Implementovat boltů

1. Odstranit existující **Bolt.cs** soubor z projektu.

2. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**. V seznamu vyberte **Boltu Storm**a zadejte **Splitter.cs** jako název. Opakujte tento postup a vytvořte druhý bolt s názvem **Counter.cs**.

   * **Splitter.cs**: Implementuje bolt, který rozdělí slova jednotlivé věty a vydá novou posloupnost slov.

   * **Counter.cs**: Implementuje bolt, který počítá každé slovo a vydá novou posloupnost slov a počty v jednotlivých slov.

     > [!NOTE]  
     > Tyto funkce bolts čtení a zápis do datových proudů, ale bolt můžete také použít ke komunikaci s zdrojů, jako jsou databáze nebo služby.

3. Otevřít **Splitter.cs**. Ve výchozím nastavení má pouze jednu metodu: **Spuštění**. Metoda spouštění je volána, když obdrží bolt řazené kolekce členů pro zpracování. Tady si můžete přečíst a zpracování příchozích řazených kolekcí členů a vygenerovat výstupní řazené kolekce členů.

4. Nahraďte obsah **rozdělovač** třídy následujícím kódem:

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

5. Otevřít **Counter.cs**a nahraďte jeho obsah třídy následujícím kódem:

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

Spoutů a boltů jsou uspořádány do grafu, který definuje tok dat mezi součástmi. Pro tuto topologii graf je následujícím způsobem:

![Diagram uspořádání součástí](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Věty jakém množství se vysílají spout a nejsou distribuovány do instance bolt rozdělovač. Rozdělovač bolt dělí vět do slov, které jsou distribuovány do boltu čítače.

Vzhledem k tomu, že počet slov uložena místně v instanci čítače, chcete, abyste měli jistotu, že konkrétní slova směrovat do stejné instance čítače bolt. Každá instance uchovává informace o konkrétních slov. Od rozdělovače bolt udržuje bez stavu, ve skutečnosti nezáleží na kterou instanci příčky obdrží které věty.

Otevřít **Program.cs**. Je důležité metoda **GetTopologyBuilder**, který se používá k definování, které je odeslána topologie Storm. Nahraďte obsah **GetTopologyBuilder** implementovat topologii je popsáno výše následujícím kódem:

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

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **odeslat do Storm v HDInsight**.

   > [!NOTE]  
   > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se k ten, který obsahuje váš cluster Storm v HDInsight.

2. Vyberte váš cluster Storm v HDInsight z **Storm Cluster** rozevíracího seznamu a pak vyberte **odeslat**. Můžete sledovat, pokud je pomocí úspěšné odeslání **výstup** okna.

3. Při úspěšném odeslání topologie **topologií Storm** pro cluster by se měla objevit. Vyberte **WordCount** topologie ze seznamu zobrazíte informace o probíhající topologii.

   > [!NOTE]  
   > Můžete také zobrazit **topologií Storm** z **Průzkumníka serveru**. Rozbalte **Azure** > **HDInsight**, klikněte pravým tlačítkem cluster Storm v HDInsight a pak vyberte **zobrazit topologie Storm**.

    Chcete-li zobrazit informace o komponentách v topologii, dvakrát klikněte na komponentu v diagramu.

4. Z **souhrn topologie** zobrazení, klikněte na tlačítko **Kill** zastavení topologie.

   > [!NOTE]  
   > Topologie Storm i nadále spouštět, dokud se deaktivuje, nebo při odstranění clusteru.

## <a name="transactional-topology"></a>Transakční topologie

Předchozí topologie je není transakční. Součástí v topologii neimplementují funkce k přehrání zprávy. Příklad transakční topologie, vytvořte projekt a vyberte **Storm ukázka** jako typ projektu.

Transakční topologie implementovat následující pro podporu opakování dat:

* **Ukládání do mezipaměti metadat**: Spout musí ukládají metadata o datech vygenerován, takže data můžete načíst a znovu aktivováno, pokud dojde k chybě. Vzhledem k tomu, že jsou data, protože ho vygeneroval vzorku malé, nezpracovaných dat pro jednotlivé řazené kolekce členů je uložen ve slovníku opětovného přehrání.

* **ACK**: Můžete volat každou bolt v topologii `this.ctx.Ack(tuple)` potvrďte, že se úspěšně zpracovala řazené kolekce členů. Pokud všechny funkce bolts potvrzeno řazené kolekce členů, `Ack` vyvolání metody spout. `Ack` Metoda umožňuje spout odstranila data, která ukládá do mezipaměti pro opakování.

* **Selhání**: Můžete volat každou bolt `this.ctx.Fail(tuple)` k označení, že zpracování se nezdařilo pro řazené kolekce členů. Selhání rozšíří na `Fail` metoda spout, kde můžete s použitím přehrály řazené kolekce členů v mezipaměti metadat.

* **ID pořadí**: Při generování řazené kolekce členů, můžete zadat ID jedinečné pořadí. Tato hodnota určuje řazené kolekce členů pro zpracování opakování (potvrzení a selhání). Například spout v **Storm ukázka** projekt používá následující při generování dat:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Tento kód emituje řazené kolekce členů obsahující větu na výchozí datový proud s hodnotou ID pořadí součástí **lastSeqId**. V tomto příkladu **lastSeqId** se zvýší, pro každý řazené kolekce členů, protože ho.

Jak je ukázáno v **Storm ukázka** projektu, určuje, zda je součást transakční mohou být nastaveny v době běhu na základě konfigurace.

## <a name="hybrid-topology-with-c-and-java"></a>Hybridní topologie pomocí jazyka C# a Javy.

Nástroje Data Lake pro Visual Studio můžete také vytvářet hybridní topologie, kde některé součásti jsou C# a jiné jsou Java.

Příklad hybridní topologie, vytvořte projekt a vyberte **Storm hybridní ukázka**. Tento typ ukázka demonstruje následující pojmy:

* **Java spout** a  **C# bolt**: Definované v **HybridTopology_javaSpout_csharpBolt**.

    * Transakční verze je definována v **HybridTopologyTx_javaSpout_csharpBolt**.

* **C#součástí spout** a **bolt jazyka Java**: Definované v **HybridTopology_csharpSpout_javaBolt**.

    * Transakční verze je definována v **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]  
  > Tato verze také ukazuje, jak použít kód Clojure z textového souboru jako součást jazyka Java.


Chcete-li přepnout topologie, který se používá, když se odešle projektu, přesuňte `[Active(true)]` příkaz topologie, kterou chcete použít, před jeho odesláním do clusteru.

> [!NOTE]  
> Všechny soubory jazyka Java, které jsou požadovány jsou k dispozici jako součást tohoto projektu v **JavaDependency** složky.

Při vytváření a odesílání hybridní topologie, zvažte následující:

* Použití **JavaComponentConstructor** k vytvoření instance třídy Java pro spout nebo funkce bolt.

* Použití **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** k serializaci dat do nebo z něj komponent v jazyce Java z objekty Java do formátu JSON.

* Při odesílání topologie do serveru, je nutné použít **další konfigurace, které** můžete zadat **cesty k souborům v jazyce Java**. Zadaná cesta by měl být adresáře, který obsahuje soubory JAR obsahující dané třídy jazyka Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Verze SCP.NET 0.9.4.203 zavádí nové třídy a metody určené konkrétně pro práci s spoutu centra událostí (spout Java, který načte ze služby Event Hubs). Při vytváření topologii, která se používá spoutu centra událostí pomocí následujících metod:

* **EventHubSpoutConfig** třídy: Vytvoří objekt, který obsahuje konfiguraci součásti spout.

* **TopologyBuilder.SetEventHubSpout** metody: Přidá komponentu spoutu centra událostí do topologie.

> [!NOTE]  
> Je nutné použít **CustomizedInteropJSONSerializer** k serializaci dat vytvářených spout.

## <a id="configurationmanager"></a>Použití ConfigurationManager

Nepoužívejte **ConfigurationManager** součásti spout a načíst hodnoty konfigurace z funkcí bolt. To může způsobit výjimku ukazatel s hodnotou null. Místo toho konfigurace pro váš projekt je předán do topologie Storm jako dvojice klíč a hodnotu v rámci topologie. Jednotlivé komponenty, která závisí na hodnoty konfigurace musíte je znovu načíst z kontextu během inicializace.

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

Pokud používáte `Get` metoda vrátí instanci komponenty, musíte zajistit, předá i `Context` a `Dictionary<string, Object>` parametry pro konstruktor. Následující příklad je základní `Get` metodu, která správně předává tyto hodnoty:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Jak aktualizovat SCP.NET

Nejnovější verze SCP.NET podporují aktualizaci balíčku prostřednictvím balíčku NuGet. Když je k dispozici nové aktualizace, zobrazí se oznámení o upgradu. Upgrade vyhledat ručně, postupujte podle těchto kroků:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.

2. Správce balíčků vyberte **aktualizace**. Pokud je k dispozici aktualizace, je uvedena. Klikněte na tlačítko **aktualizace** balíčku k její instalaci.

> [!IMPORTANT]  
> Pokud váš projekt byl vytvořen v dřívější verzi SCP.NET, který nepoužívá NuGet, je třeba provést následující kroky a aktualizujete na novější verzi:
>
> 1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**.
> 2. Použití **hledání** pole, vyhledejte a pak přidejte **Microsoft.scp.NET.SDK, který** do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Řešení běžných potíží s topologií

### <a name="null-pointer-exceptions"></a>Výjimky ukazatel s hodnotou Null

Při použití topologií v jazyce C# s clusterem HDInsight založených na Linuxu, funkce bolt a součástí spout komponent, které používají **ConfigurationManager** číst nastavení konfigurace v době běhu může vrátit ukazatel s hodnotou null výjimky.

Konfigurace pro váš projekt je předán do topologie Storm jako dvojice klíč a hodnotu v rámci topologie. Se dá načíst z objektu slovník, který je předán vaše komponenty, když jsou inicializovány.

Další informace najdete v tématu [ConfigurationManager](#configurationmanager) část tohoto dokumentu.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Při použití topologií v jazyce C# s clusterem HDInsight založených na Linuxu, může dojít k následující chybě:

    System.TypeLoadException: Failure has occurred while loading a type.

Tato chyba nastane, pokud používáte binární soubor, který není kompatibilní s verzí rozhraní .NET, který podporuje Mono.

Pro clustery HDInsight založené na Linuxu Ujistěte se, že váš projekt používá binární soubory zkompilovány pro rozhraní .NET 4.5.

### <a name="test-a-topology-locally"></a>Testovací topologie s místně

I když je snadné nasazení topologie do clusteru, v některých případech budete muset testovací topologie s místně. Pomocí následujících kroků spustit a otestovat topologii příkladu v tomto kurzu místně ve vašem vývojovém prostředí.

> [!WARNING]  
> Místní testování funguje jenom pro basic, C# – pouze topologie. Nemůžete použít místní testování hybridní topologie nebo topologie, které využívají více datových proudů.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **vlastnosti**. Ve vlastnostech projektu změnit **typ výstupu** k **konzolovou aplikaci**.

    ![Snímek obrazovky vlastností projektu, se zvýrazněným typem výstupu](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Nezapomeňte změnit **typ výstupu** zpět **knihovny tříd** před nasazením topologie do clusteru.

2. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak vyberte **přidat** > **nová položka**. Vyberte **třídy**a zadejte **LocalTest.cs** jako název třídy. Nakonec klikněte na tlačítko **přidat**.

3. Otevřít **LocalTest.cs**a přidejte následující **pomocí** příkazu v horní části:

    ```csharp
    using Microsoft.SCP;
    ```

4. Použijte následující kód jako obsah **LocalTest** třídy:

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

    Za chvíli si v komentářích ke kódu. Tento kód používá **LocalContext** pro spuštění komponent ve vývojovém prostředí a opakuje datový proud mezi komponentami do textových souborů na místním disku.

1. Otevřít **Program.cs**a přidejte následující text do **hlavní** metody:

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

2. Uložte změny a pak klikněte na tlačítko **F5** nebo vyberte **ladění** > **spustit ladění** ke spuštění projektu. Okna konzoly by měl zobrazit a stavu protokolu průběh testů. Když **dokončené testy** se zobrazí, stisknutím jakékoli klávesy zavřete okno.

3. Použití **Windows Explorer** vyhledejte adresář, který obsahuje váš projekt. Příklad: **C:\Users\<vaše_uživatelské_jméno > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. V tomto adresáři otevřete **Bin**a potom klikněte na tlačítko **ladění**. Zobrazí se textové soubory, které byly vytvořeny při provádění testů: sentences.txt counter.txt a splitter.txt. Otevřete každý soubor textového a kontrolovat data.

   > [!NOTE]  
   > Data řetězce se ukládá jako pole desetinných hodnot v těchto souborech. Například \[[97,103,111]] v **splitter.txt** soubor je slovo *a*.

> [!NOTE]  
> Nezapomeňte nastavit **typ projektu** zpět **knihovny tříd** před nasazením do Storm v clusteru HDInsight.

### <a name="log-information"></a>Informace o protokolu

Můžete snadno protokolovat informace z komponentami vaší topologii s použitím `Context.Logger`. Například následující příkaz vytvoří položku informační protokolu:

```csharp
Context.Logger.Info("Component started");
```

Zaznamenané informace můžete zobrazit z **protokol služby Hadoop**, která byla nalezena v **Průzkumníka serveru**. Rozbalte položku pro váš cluster Storm v HDInsight a pak rozbalte **protokol služby Hadoop**. Nakonec vyberte soubor protokolu, chcete-li zobrazit.

> [!NOTE]  
> Protokoly se ukládají v účtu úložiště Azure, který se používá ve vašem clusteru. Pokud chcete zobrazit protokoly v sadě Visual Studio, musíte se přihlásit k předplatnému Azure, který je vlastníkem účtu úložiště.

### <a name="view-error-information"></a>Zobrazit informace o chybě

Chcete-li zobrazit chyby, ke kterým došlo v probíhající topologii, postupujte následovně:

1. Z **Průzkumníka serveru**, klikněte pravým tlačítkem cluster Storm v HDInsight a pak vyberte **topologií Storm zobrazení**.

2. Pro **Spout** a **Bolts**, **poslední chyba** sloupec obsahuje informace o poslední chybě.

3. Vyberte **Spout Id** nebo **Bolt Id** pro komponentu, která obsahuje chybu uvedené. Na stránce s podrobnostmi, která se zobrazí se další chybové informace uvedené v **chyby** části v dolní části stránky.

4. Chcete-li získat další informace, vyberte **Port** z **prováděcí moduly** části stránky naleznete v protokolu Storm pracovního procesu za posledních několik minut.

### <a name="errors-submitting-topologies"></a>Chyby odesílání topologie

Pokud narazíte na chyby odesílání topologie do HDInsight, můžete najít protokoly pro komponenty na straně serveru, které zpracovávají odesílání topologie ve vašem clusteru HDInsight. K načtení těchto protokolů, použijte následující příkaz z příkazového řádku:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Nahraďte __sshuser__ pomocí uživatelského účtu SSH pro cluster. Nahraďte __clustername__ s názvem clusteru HDInsight. Další informace o používání `scp` a `ssh` s HDInsight, naleznete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Příspěvky může selhat z několika důvodů:

* JDK není nainstalován nebo není v cestě.
* V odesílání zahrnuté nejsou požadované závislosti jazyka Java.
* Nekompatibilní závislostí.
* Duplicitní názvy topologie.

Pokud `hdinsight-scpwebapi.out` protokol obsahuje `FileNotFoundException`, může to být způsobeno následující podmínky:

* Sadu JDK není v cestě ve vývojovém prostředí. Ověřte, že je sada JDK nainstalovaná ve vývojovém prostředí a který `%JAVA_HOME%/bin` v cestě.
* Chybí závislost Java. Ujistěte se, že všechny soubory .jar požadované jsou včetně jako část odeslání.

## <a name="next-steps"></a>Další postup

Příklad zpracování dat ze služby Event Hubs najdete v tématu [zpracovávat události z Azure Event Hubs se Stormem v HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Příklad topologie C#, která rozdělí Streamovat data do různých datových proudů, naleznete v tématu [příklad jazyka C# Storm](https://github.com/Blackmist/csharp-storm-example).

Pokud chcete zjistit další informace o vytváření topologií C#, přečtěte si [Githubu](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Další Storm v HDInsight ukázky a další způsoby práce s HDInsight najdete v následujících dokumentech:

**Microsoft SCP.NET**

* [Průvodce programováním pro spojovací bod služby](apache-storm-scp-programming-guide.md)

**Apache Storm v HDInsight**

* [Nasazení a monitorování topologií pomocí Apache Storm v HDInsight](apache-storm-deploy-monitor-topology.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)

**Apache Hadoop v HDInsight**

* [Použití Apache Hivu s Apache Hadoop v HDInsight](../hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig s Apache Hadoop v HDInsight](../hadoop/hdinsight-use-pig.md)
* [Použití Apache Hadoop MapReduce s Apache Hadoop v HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase v HDInsight**

* [Začínáme s Apache HBase v HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
