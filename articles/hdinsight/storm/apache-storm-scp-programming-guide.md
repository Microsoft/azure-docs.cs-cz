---
title: Průvodce programováním v SCP.NET pro zaplavení ve službě Azure HDInsight
description: Naučte se používat SCP.NET k vytvoření. Topologie nenáročného zaplavení pro použití s více operačními systémy v Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: c993b3f70f609fb79c51ba9be08fa3d5dc7e8317
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864104"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Průvodce programováním SCP pro Apache Storm ve službě Azure HDInsight

SCP je platforma pro sestavování v reálném čase, spolehlivé, konzistentní a vysoce výkonné aplikace pro zpracování dat. Je postavená na [Apache Storm](https://storm.incubator.apache.org/), což je systém pro zpracování datových proudů, který je navržený Open Source softwarovými komunitami. Nathan Marz vytvořila více než jednou. Služba Twitter byla publikována jako open source. Nevyužívá [Apache Zookeeper](https://zookeeper.apache.org/), což je další projekt Apache, který umožňuje vysoce spolehlivou distribuovanou koordinaci a správu stavu.

Projekt spojovacího bodu služby má v systému Windows port, ale také rozšíření a přizpůsobení pro prostředí systému Windows. Mezi tato rozšíření patří prostředí pro vývojáře .NET a knihovny .NET. Vlastní nastavení zahrnuje nasazení založené na systému Windows.

S rozšířeními a přizpůsobeními nemusíte rozvětvit Open Source softwarové projekty. Můžete použít odvozená prostředí, která jsou postavená na sobě.

## <a name="processing-model"></a>Model zpracování

Data ve spojovacím bodu služby jsou modelována jako kontinuální streamování řazených kolekcí členů. Řazené kolekce členů jsou obvykle:

1. Flow do fronty.
1. Jsou vyzvednuty a transformovány pomocí obchodní logiky, která je hostována v topologii.
1. Buď mají výstup v podobě řazené kolekce členů do jiného systému SCP, nebo se ukládají do úložišť, jako jsou distribuované souborové systémy a databáze, jako je SQL Server.

:::image type="content" source="./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png" alt-text="Diagram dat s dořazením do fronty pro zpracování, který v systému zapíná úložiště dat" border="false":::

V zaplavení topologie aplikace definuje graf výpočtu. Každý uzel v topologii obsahuje logiku zpracování. Propojení mezi uzly označují tok dat.

Uzly, které vkládají vstupní data do topologie, se nazývají _spoutů_. Můžete je použít ke sekvencování dat. Vstupní data mohou pocházet ze zdroje, jako jsou protokoly souborů, transakční databáze nebo čítač výkonu systému.

Uzly, které mají vstupní i výstupní datové toky, se nazývají _šrouby_. Vlastní filtrování, výběry a agregace dat.

SCP podporuje nejlepší úsilí, nejméně jednou a právě po zpracování dat.

V aplikaci pro zpracování distribuovaných datových proudů může dojít k chybám při zpracování dat. Takové chyby zahrnují výpadek sítě, selhání počítače nebo chybu ve vašem kódu. Při nesprávném zpracování se všechna data zpracovávají alespoň jednou při automatickém přehrání stejných dat, když dojde k chybě.

Nejméně jeden proces zpracování je jednoduchý a spolehlivý a vyhovuje mnoha aplikacím. Ale pokud aplikace vyžaduje přesné počítání, je zpracování nejméně jednou nedostatečné, protože stejná data by mohla být přehrána v topologii aplikace. V takovém případě se přesně po zpracování ověří výsledek i v případě, že jsou data znovu přehrána a zpracována vícekrát.

Spojovací bod služby umožňuje vývojářům rozhraní .NET vytvářet aplikace pro zpracování dat v reálném čase při použití prostředí Java Virtual Machine (JVM) se zachováním. JVM a .NET komunikují prostřednictvím místních soketů TCP. Každá Spout/šroub je dvojice procesů .NET/Java, kde je logika uživatele spouštěna v procesu .NET jako modul plug-in.

Chcete-li vytvořit aplikaci pro zpracování dat v rámci spojovacího bodu služby, postupujte podle následujících kroků:

1. Navrhněte a implementujte spoutů pro vyžádání dat z front.
1. Navrhněte a implementujte šrouby, které zpracovávají vstupní data a ukládají je do externích úložišť, jako je databáze.
1. Navrhněte topologii a potom ji odešlete a spusťte.

Topologie definuje vrcholy a data, která jsou mezi nimi natéká. Spojovací bod služby převezme specifikaci topologie a nasadí ji do clusteru s více podmnožinami, kde každý vrchol běží na jednom logickém uzlu. Plánovač úloh vyplavování se stará o převzetí služeb při selhání a škálování.

Tento článek používá několik jednoduchých příkladů, pomocí kterých můžete sestavovat aplikace pro zpracování dat pomocí spojovacího bodu služby.

## <a name="scp-plug-in-interface"></a>Rozhraní modulu plug-in spojovacího bodu služby

Moduly plug-in spojovacího bodu služby jsou samostatné aplikace. Můžou běžet v rámci aplikace Visual Studio během vývoje a musí se po produkčním nasazení připojit k kanálu zaplavení.

Zápis modulu plug-in spojovacího bodu služby je stejný jako při psaní jakékoli jiné konzolové aplikace pro Windows. Platforma SCP.NET deklaruje některá rozhraní pro Spout/šroub. Kód modulu plug-in implementuje tato rozhraní. Hlavním účelem tohoto návrhu je, abyste se mohli zaměřit na obchodní logiku a zároveň umožnit SCP.NET platformě.

Váš kód modulu plug-in implementuje jedno z následujících rozhraní. Které rozhraní závisí na tom, zda je topologie transakční, nebo netransakční a zda je komponenta Spout nebo šroub.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** je společné rozhraní pro mnoho modulů plug-in. V současné době se jedná o fiktivní rozhraní.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** je rozhraní pro netransakční Spout.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Při volání **NextTuple** může váš kód jazyka C# vygenerovat jednu nebo více řazených kolekcí členů. Pokud není nic k vygenerování, tato metoda by se měla vrátit bez nutnosti vysílat nic.

Metody **NextTuple**, **ACK** a **selžou** jsou všechny volány v těsné smyčce v jednom vlákně procesu C#. Pokud neexistují žádné řazené kolekce členů k vygenerování, **NextTuple** spánku po krátkou dobu, jako je 10 milisekund. Tento režim spánku pomáhá vyhnout se plýtvání dostupností procesoru.

Metody **ACK** a **selžou** se volají pouze v případě, že soubor specifikace povoluje mechanismus potvrzení. Parametr *seqId* identifikuje řazenou kolekci členů, která je potvrzena nebo se nezdařila. Pokud je potvrzení povoleno v netransakční topologii, je třeba použít následující funkci **Emit** v Spout:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Pokud netransakční topologie nepodporuje potvrzení, **potvrzení** a **selhání** mohou být ponechány jako prázdné funkce.

Vstupní parametr *parametry* v těchto funkcích určuje prázdný slovník a je vyhrazen pro budoucí použití.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** je rozhraní pro netransakční šroub.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Je-li k dispozici nová řazená kolekce členů, je volána funkce **Execute** pro její zpracování.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** je rozhraní transakčního Spout.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Stejně jako jejich netransakční protějšky, **NextTx**, **ACK** a **selhání** jsou všechny volány v těsné smyčce v jednom vlákně procesu C#. Pokud neexistují žádné řazené kolekce členů k vygenerování, **NextTx** spánku po krátkou dobu, jako je 10 milisekund. Tento režim spánku pomáhá vyhnout se plýtvání dostupností procesoru.

Když je volána metoda **NextTx** ke spuštění nové transakce, výstupní parametr *seqId* identifikuje transakci. Transakce se používá také v poli **ACK** a **selhání**. Metoda **NextTx** může emitovat data na stranu Java. Data se ukládají do ZooKeeper pro podporu opětovného přehrání. Vzhledem k tomu, že ZooKeeper má omezené kapacity, váš kód by měl generovat pouze metadata a nikoli Hromadná data v transakčních Spout.

Vzhledem k tomu, že operace **odolnosti** automaticky přehrává neúspěšnou transakci, obvykle se nevolá. Pokud však SCP může kontrolovat metadata vygenerovaná transakčním Spout, může volání **selhat** , pokud metadata nejsou platná.

Vstupní parametr *parametry* v těchto funkcích určuje prázdný slovník a je vyhrazen pro budoucí použití.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** je rozhraní pro transakční šroub.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Metoda **Execute** se volá, když se do šroubu dorazí nová řazená kolekce členů. Metoda **FinishBatch** je volána, když tato transakce skončí. Vstupní parametr *parametry* je vyhrazen pro budoucí použití.

V případě transakční topologie je **StormTxAttempt** důležitou třídou. Má dva členy: **TxId** a **AttemptId**. Člen **TxId** identifikuje konkrétní transakci. Transakce se může pokusit několikrát, pokud se nezdaří a dojde k jejímu přehrání.

SCP.NET vytvoří nový objekt **ISCPBatchBolt** pro zpracování každého objektu **StormTxAttempt** stejným způsobem jako v jazyce Java. Účelem tohoto návrhu je podpora paralelního zpracování transakcí. Po dokončení pokusu o transakci dojde ke zničení odpovídajícího objektu **ISCPBatchBolt** a uvolnění paměti.

## <a name="object-model"></a>Objektový model

SCP.NET také poskytuje jednoduchou sadu klíčových objektů, pomocí kterých můžou vývojáři programovat. Objekty jsou **Context**, **úložiště stavu SMP** a **SCPRuntime**. Jsou popsány v této části.

### <a name="context"></a>Kontext

**Kontextový** objekt poskytuje běžící prostředí pro aplikaci. Každá instance **ISCPPlugin** třídy **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** nebo **ISCPBatchBolt** má odpovídající **kontextovou** instanci. Funkce poskytovaná **kontextem** je rozdělena na tyto dvě části:

* Statická část, která je k dispozici v celém procesu C#
* Dynamická část, která je k dispozici pouze pro konkrétní instanci **kontextu**

### <a name="static-part"></a>Statická část

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

Objekt **protokolovacího** nástroje je k dispozici pro účely protokolování.

Objekt **pluginType** označuje typ modulu plug-in procesu C#. Pokud je proces spuštěn v místním testovacím režimu bez Java, je typ modulu plug-in **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

Vlastnost **Konfigurace** Získá konfigurační parametry ze strany Java, která je předává při inicializaci modulu plug-in jazyka C#. Parametry **Konfigurace** jsou rozděleny do dvou částí: **stormConf** a **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

Část **stormConf** je definována jako parametry, které jsou definovány parametrem pro vytvoření a část **pluginConf** je parametry definované spojovacím bod služby. Tady je příklad:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

Typ **TopologyContext** Získá kontext topologie. Je nejužitečnější pro více paralelních komponent. Tady je příklad:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Dynamická část

Následující rozhraní jsou relevantní pro určitou instanci **kontextu** , která je vytvořena platformou SCP.NET a předána do vašeho kódu:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Pro netransakční Spout, který podporuje potvrzení, je k dispozici následující metoda:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Netransakční pole, které podporuje potvrzení, by mělo explicitně volat **ACK** nebo **selhat** s řazenou kolekcí členů, kterou přijal. Při generování nové řazené kolekce členů musí šroub určovat také kotvy řazené kolekce členů. K dispozici jsou následující metody:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>Úložiště stavu SMP

Objekt **úložiště stavu SMP** poskytuje služby metadat, generování sekvence monotónní a koordinaci bez čekání. V **úložiště stavu SMP** můžete vytvořit distribuované abstrakce souběžnosti na vyšší úrovni. Mezi tyto abstrakce patří distribuované zámky, distribuované fronty, překážky a transakční služby.

Aplikace spojovacího bodu služby mohou použít objekt **State** k serializaci informací v [Apache Zookeeper](https://zookeeper.apache.org/). Tato možnost je obzvláště užitečná pro transakční topologii. Pokud transakční Spout přestane odpovídat a restartuje, může **stav** načíst potřebné informace z Zookeeper a restartovat kanál.

Objekt **úložiště stavu SMP** má tyto hlavní metody:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Objekt **State** má tyto hlavní metody:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Pokud je **simpleMode** nastaveno na **hodnotu true**, metoda **Commit** odstraní odpovídající ZNode v Zookeeper. V opačném případě metoda odstraní aktuální ZNode a přidá nový uzel do SVĚŘENé \_ cesty.

### <a name="scpruntime"></a>SCPRuntime

Třída **SCPRuntime** poskytuje následující dvě metody:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Metoda **Initialize** inicializuje běhové prostředí SPOJOVACÍho bodu služby. V této metodě se proces C# připojí k straně Java a získá konfigurační parametry a kontext topologie.

Metoda **LaunchPlugin** spustí smyčku zpracování zpráv. V této smyčce modul plug-in jazyka C# přijímá zprávy ze strany Java. Tyto zprávy obsahují řazené kolekce členů a řídicí signály. Modul plug-in potom zpracuje zprávy, například voláním metody rozhraní poskytované vaším kódem.

Vstupní parametr pro **LaunchPlugin** je delegát. Metoda může vracet objekt, který implementuje rozhraní **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** nebo **ISCPBatchBolt** .

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Pro **ISCPBatchBolt** můžete získat objekt **StormTxAttempt** z parametru *parametry* a použít ho k posouzení, zda se jedná o pokusy o přehrání. Pokus o opakované přehrání se často provádí na potvrzovacím poli. Tato kontrolní HelloWorldTx ukazuje příklad dále v tomto článku.

Moduly plug-in spojovacího bodu služby se obvykle spouštějí ve dvou režimech: místní testovací režim a běžný režim.

#### <a name="local-test-mode"></a>Místní testovací režim

V tomto režimu se moduly plug-in SCP v kódu C# spouští během fáze vývoje v rámci sady Visual Studio. V tomto režimu můžete použít rozhraní **ILocalContext** . Rozhraní poskytuje metody pro serializaci vygenerovaných řazených kolekcí členů do místních souborů a jejich čtení zpátky do paměti RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Běžný režim

V tomto režimu spouští proces zaplavování Java moduly plug-in SCP. Tady je příklad:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Jazyk specifikace topologie

Specifikace topologie spojovacího bodu služby je jazyk specifický pro doménu (DSL), který popisuje a konfiguruje topologie SCP. Vychází z [CLOJURE DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) na více procesorech a je rozšířena pomocí SPOJOVACÍho bodu služby.

Specifikace topologie můžete odesílat přímo do clusteru s více podsystému, aby bylo možné je spustit pomocí příkazu **runSpec** .

SCP.NET přidal následující funkce pro definování transakčních topologií:

| Nová funkce | Parametry | Description |
| --- | --- | --- |
| **TX – topolopy** |*název topologie*<br />*Spout – mapa*<br />*Mapa šroubů* |Definuje transakční topologii s názvem topologie, mapou definice spoutů a mapou definice šrouby. |
| **SCP – TX-Spout** |*Exec – název*<br />*argumentů*<br />*pole* |Definuje transakční Spout. Funkce spustí aplikaci, která je určena pomocí *exec-Name* a používá *args*.<br /><br />Parametr *Fields* určuje výstupní pole pro Spout. |
| **SCP – TX-Batch-šroub** |*Exec – název*<br />*argumentů*<br />*pole* |Definuje transakčního dávkovacího šroubu. Funkce spustí aplikaci, která je určena pomocí *exec-Name* a používá *args.*<br /><br />Parametr *Fields* určuje výstupní pole pro šroub. |
| **SCP – TX-Commit-šroub** |*Exec – název*<br />*argumentů*<br />*pole* |Definuje hodnotu transakčního potvrzení. Funkce spustí aplikaci, která je určena pomocí *exec-Name* a používá *args*.<br /><br />Parametr *Fields* určuje výstupní pole pro šroub. |
| **nontx – topologie** |*název topologie*<br />*Spout – mapa*<br />*Mapa šroubů* |Definuje netransakční topologii s názvem topologie, mapou definice spoutů a mapou definice šrouby. |
| **SCP – Spout** |*Exec – název*<br />*argumentů*<br />*pole*<br />*ukazatelů* |Definuje netransakční Spout. Funkce spustí aplikaci, která je určena pomocí *exec-Name* a používá *args*.<br /><br />Parametr *Fields* určuje výstupní pole pro Spout.<br /><br />Parametr *Parameters* je nepovinný. Použijte ji k zadání parametrů jako "netransakční. ACK. Enabled". |
| **SCP – šroub** |*Exec – název*<br />*argumentů*<br />*pole*<br />*ukazatelů* |Definuje netransakční šroub. Funkce spustí aplikaci, která je určena pomocí *exec-Name* a používá *args*.<br /><br />Parametr *Fields* určuje výstupní pole pro šroub<br /><br />Parametr *Parameters* je nepovinný. Použijte ji k zadání parametrů jako "netransakční. ACK. Enabled". |

SCP.NET definuje následující klíčová slova:

| Klíčové slovo | Description |
| --- | --- |
| **: název** |Název topologie |
| **: topologie** |Topologie pomocí funkcí v předchozí tabulce a integrovaných funkcích |
| **:p** |Pomocný parametr paralelismus pro každý Spout nebo šroub |
| **: config** |Jestli se mají nakonfigurovat parametry nebo aktualizovat existující |
| **: schéma** |Schéma datového proudu |

SCP.NET také definuje tyto často používané parametry:

| Parametr | Popis |
| --- | --- |
| "plugin.name" |Název souboru. exe modulu plug-in C# |
| plugin. args |Argumenty modulu plug-in |
| Output. Schema |Výstupní schéma |
| "netransakční. ACK. Enabled" |Zda je povoleno potvrzení pro netransakční topologii |

Příkaz **runSpec** je nasazený společně s bity. Toto je použití příkazu:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Parametr *Resource-dir* je nepovinný. Tuto hodnotu zadejte, pokud chcete připojit aplikaci v jazyce C#. Zadaný adresář obsahuje aplikaci, závislosti a konfigurace.

Parametr *classpath* je také volitelný. Určuje cestu třídy Java, pokud soubor specifikace obsahuje Java Spout nebo šroub.

## <a name="miscellaneous-features"></a>Různé funkce

### <a name="input-and-output-schema-declarations"></a>Vstupní a výstupní deklarace schématu

Vaše procesy v jazyce C# mohou generovat řazené kolekce členů. K tomu platforma serializace řazené kolekce členů do objektů **Byte []** a přenáší objekty na stranu Java. Potom přenáší tyto řazené kolekce členů na cíle.

V rámci podřízených komponent procesy C# dostávají řazené kolekce členů zpátky ze strany Java a převádí je na původní typy platformy. Všechny tyto operace jsou pro platformu skryté.

Aby bylo možné podporovat serializaci a deserializaci, váš kód musí deklarovat schéma vstupu a výstupu. Schéma je definováno jako slovník. ID streamu je klíč slovníku. Hodnota klíče je typy sloupců. Komponenta může deklarovat více datových proudů.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

Následující funkce je přidána do objektu **kontextu** :

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Vývojáři musí zajistit, aby emitované řazené kolekce členů řídily schéma definované pro datový proud. V opačném případě bude systém generovat výjimku za běhu.

### <a name="multistream-support"></a>Podpora více proudů

Spojovací bod služby umožňuje, aby kód vygeneroval nebo přijímal z více různých datových proudů současně. **Kontextový** objekt odráží tuto podporu jako nepovinný parametr ID datového proudu metody **Emit** .

Byly přidány dvě metody v objektu **kontextu** SCP.NET. Emitují jednu nebo více řazených kolekcí členů k určitým datovým proudům. Parametr *streamId* je řetězec. Jeho hodnota musí být stejná v kódu C# i specifikace definice topologie.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Emitování do neexistujícího datového proudu způsobí výjimky za běhu.

### <a name="fields-grouping"></a>Seskupení polí

Vestavěná seskupení polí v zaplavování v SCP.NET nefunguje správně. Na straně proxy serveru Java je datový typ všech polí ve skutečnosti **Byte []**. Seskupení polí používá kód hash objektu **Byte []** k seskupení. Kód hash je adresa tohoto objektu v paměti RAM. Takže seskupení bude špatné pro vícebajtové objekty, které sdílejí stejný obsah, ale ne stejnou adresu.

SCP.NET přidá přizpůsobenou metodu seskupení a k seskupení používá obsah objektu **Byte []** . V souboru specifikace syntaxe vypadá jako v tomto příkladu:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

V předchozím souboru specifikace:

* `scp-field-group` Určuje, že seskupení je přizpůsobené seskupení polí implementované spojovacím bod služby.
* `:tx` nebo `:non-tx` Určuje, zda je topologie transakční. Tyto informace budete potřebovat, protože počáteční index se liší od transakčních a netransakčních topologií.
* `[0,1]` Určuje sadu hodnot hash pro ID polí začínající nulou.

### <a name="hybrid-topology"></a>Hybridní topologie

Nativní kód pro kódování je napsán v jazyce Java. SCP.NET má vylepšenou funkci pro vytváření kódu v jazyce C#, která vám umožní zpracovat obchodní logiku. Ale SCP.NET podporuje i hybridní topologie, které obsahují nejen C# spoutů/šrouby, ale také Java spoutů/šrouby.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Určení Java Spout/šroub v souboru specifikace

Pomocí **spojovacího bodu služby SCP – Spout** a **SCP-šroub** můžete zadat v souboru specifikace spoutů a šrouby Java. Tady je příklad:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Tady `microsoft.scp.example.HybridTopology.Generator` je název třídy Java Spout.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Určení cesty tříd Java v příkazu runSpec

Pokud chcete odeslat topologii, která obsahuje Java spoutů nebo šrouby, napřed je nakompilujte, aby se vytvořily soubory JAR. Pak určete cestu třídy Java, která obsahuje soubory JAR při odeslání topologie. Tady je příklad:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Tady `examples\HybridTopology\java\target\` je složka obsahující soubor JAR Spout/šroub Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializace a deserializace mezi Java a C #

Komponenta SCP obsahuje stranu Java a stranu C#. Pro interakci s nativním rozhraním Java spoutů/šrouby musí být serializace a deserializace provedena mezi stranou Java a straně C#, jak je znázorněno v následujícím grafu:

:::image type="content" source="./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png" alt-text="Diagram součásti jazyka Java odesílaných do komponenty spojovacího bodu služby, která pak odesílá do jiné komponenty Java" border="false":::

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serializace na straně Java a deserializace na straně C#

Nejdřív zadejte výchozí implementaci serializace na straně a deserializaci Java na straně C#.

Zadejte metodu serializace na straně Java v souboru specifikace.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

V kódu jazyka C# určete metodu deserializace na straně jazyka C#.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Pokud datový typ není příliš složitý, měla by tato výchozí implementace zpracovat většinu případů. Tady jsou případy, kdy můžete připojit vlastní implementaci:

* Datový typ je pro výchozí implementaci příliš složitý.
* Výkon vaší výchozí implementace nevyhovuje vašim požadavkům.

Rozhraní serializace na straně Java je definováno jako:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

Rozhraní deserializace na straně jazyka C# je definováno jako:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serializace v části a deserializace v jazyce C# na straně Java

V kódu jazyka C# určete metodu serializace na straně jazyka C#.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Zadejte metodu deserializace na straně Java v souboru specifikace.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Zde `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` je název deserializace a `"microsoft.scp.example.HybridTopology.Person"` je cílovou třídou, do které jsou data deserializována.

Můžete také připojit vlastní implementaci serializátoru jazyka C# a deserializaci Java.

Tento kód je rozhraní serializátoru jazyka C#:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Tento kód je rozhraní pro deserializátor Java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Režim hostitele spojovacího bodu služby

V tomto režimu můžete zkompilovat kód jako knihovnu DLL a použít SCPHost.exe, jak poskytuje spojovací bod služby (SCP) k odeslání topologie. Soubor specifikace vypadá jako tento kód:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Zde `"plugin.name"` je zadán jako `"SCPHost.exe"` , který je poskytován sadou SCP SDK. SCPHost.exe přijímá tři parametry v tomto pořadí:

1. Název knihovny DLL, který je `"HelloWorld.dll"` v tomto příkladu.
1. Název třídy, který je `"Scp.App.HelloWorld.Generator"` v tomto příkladu.
1. Název veřejné statické metody, kterou lze vyvolat pro získání instance třídy **ISCPPlugin**.

V režimu hostitele zkompilujte kód jako knihovnu DLL pro vyvolání platformou SCP. Vzhledem k tomu, že platforma pak může získat úplnou kontrolu nad celou logikou zpracování, doporučujeme odeslat topologii v režimu hostitele spojovacího bodu služby. Tím se zjednoduší vývojové prostředí. Přináší vám taky větší flexibilitu a lepší zpětnou kompatibilitu pro pozdější verze.

## <a name="scp-programming-examples"></a>Příklady programování SCP

### <a name="helloworld"></a>Hell

Následující příklad jednoduchého HelloWorld ukazuje na SCP.NETu. Používá netransakční topologii s Spout nazvaný **generátor** a dvěma šrouby s názvem **rozdělovač** a **čítač**. **Generátor** Spout náhodně generuje věty a vygeneruje tyto věty do **rozdělovače**. Šroubce **rozdělovač** rozdělí věty na slova a vygeneruje tato slova na **čítač** . **Čítač počítadla** používá slovník k záznamu výskytu každého slova.

V tomto příkladu jsou dva soubory specifikace: HelloWorld. spec a HelloWorld \_ EnableAck. spec. Kód jazyka C# může zjistit, zda je potvrzení povoleno získáním `pluginConf` objektu ze strany Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Pokud je potvrzení povoleno v Spout, slovník ukládá do mezipaměti řazené kolekce členů, které nebyly potvrzeny. Pokud `Fail` je volána, je znovu přehrána neúspěšná řazená kolekce členů.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

Následující příklad HelloWorldTx ukazuje, jak implementovat transakční topologii. Příklad obsahuje jeden Spout nazvaný **generátor**, Batch se nazývá **částečný počet** a potvrzovací šroub s názvem **Count-suma**. Příklad také obsahuje tři existující textové soubory: DataSource0.txt, DataSource1.txt a DataSource2.txt.

V každé transakci **generátor** Spout náhodně vybere dva soubory z existujících tří souborů a vygeneruje názvy dvou souborů do šroubů s **částečným počtem** . Šroub **částečného počtu** :

1. Získá název souboru z přijaté řazené kolekce členů.
1. Otevře odpovídající soubor.
1. Spočítá počet slov v souboru.
1. Vygeneruje počet slov v poli **Count-suma** .

Hodnota **čítače Count-suma** shrnuje celkový počet.

Chcete-li dosáhnout přesně jedné sémantiky, musí být v poli **počet-součet** potvrzení, zda se jedná o přehrajte transakci. V tomto příkladu má následující statickou členskou proměnnou:

```csharp
public static long lastCommittedTxId = -1; 
```

Když je vytvořena instance **ISCPBatchBolt** , získá hodnotu `txAttempt` objektu ze vstupních parametrů.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Když `FinishBatch` je volána, `lastCommittedTxId` je aktualizována, pokud není transakce znovu přehrána.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Tato topologie obsahuje Java Spout a jazyk C#. Používá výchozí serializaci a deserializaci, která je poskytována platformou SCP. Podrobnosti souboru specifikace najdete v souboru HybridTopology. spec ve složce Examples \\ HybridTopology. Viz také SubmitTopology.bat, jak zadat cestu třídy Java.

### <a name="scphostdemo"></a>SCPHostDemo

Tento příklad je v podstatě stejný jako HelloWorld. Jediným rozdílem je, že váš kód je kompilován jako knihovna DLL a topologie je odeslána pomocí SCPHost.exe. Podrobnější vysvětlení najdete v části režim hostitele spojovacího bodu služby.

## <a name="next-steps"></a>Další kroky

Příklady topologií Apache Storm vytvořených pomocí spojovacího bodu služby (SCP) najdete v následujících článcích:

* [Vývoj topologií v jazyce C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Zpracování událostí z Azure Event Hubs s využitím Apache Storm ve službě HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Zpracování dat snímače vozidla z Event Hubs pomocí Apache Storm v HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrakce, transformace a načítání (ETL) z Azure Event Hubs do Apache HBA](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
