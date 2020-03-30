---
title: SCP.NET programovací průvodce stormem v Azure HDInsightu
description: Přečtěte si, jak pomocí SCP.NET vytvořit . Topologie Storm založené na NET pro použití s Storm spuštěným v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186865"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Programovací průvodce SCP pro Apache Storm v Azure HDInsight

SCP je platforma pro vytváření aplikací pro zpracování dat v reálném čase, spolehlivé, konzistentní a vysoce výkonné aplikace pro zpracování dat. Je postaven na vrcholu [Apache Storm](https://storm.incubator.apache.org/), což je stream-zpracování systému navržený open-source softwarových komunit. Nathan Marz vytvořil Storm. To bylo zveřejněno jako open source twitter. Storm používá [Apache ZooKeeper](https://zookeeper.apache.org/), což je další projekt Apache, který umožňuje vysoce spolehlivou distribuovanou koordinaci a správu stavu.

Projekt SCP portoval nejen Storm v systému Windows, ale také rozšíření s přidanými projekty a přizpůsobení pro prostředí systému Windows. Rozšíření zahrnují prostředí pro vývojáře rozhraní .NET a knihovny .NET. Vlastní nastavení zahrnuje nasazení na základě systému Windows.

S rozšířeními a přizpůsobením nemusíte rozvinit softwarové projekty s otevřeným zdrojovým kódem. Můžete použít odvozené prostředí, které jsou postaveny na storm.

## <a name="processing-model"></a>Model zpracování

Data v SCP je modelován jako průběžné datové proudy řazené kolekce členů. N-tic obvykle n-tic:

1. Tok do fronty.
1. Jsou zvednuty a transformovány obchodní logikou hostovoci uvnitř topologie Storm.
1. Buď mají svůj výstup piped jako řazené kolekce členů do jiného systému SCP nebo jsou potvrzeny do úložišť, jako jsou distribuované souborové systémy a databáze jako SQL Server.

![Diagram fronty, která dodává data ke zpracování, což zase přivádí úložiště dat](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

V stormu definuje topologie aplikace výpočetní graf. Každý uzel v topologii obsahuje logiku zpracování. Propojení mezi uzly označují tok dat.

Uzly, které vstřikují vstupní data do topologie, se nazývají _výtoky_. Můžete je použít k sekvenci dat. Vstupní data mohou pocházet ze zdroje, jako jsou protokoly souborů, transakční databáze nebo čítač výkonu systému.

Uzly, které mají vstupní i výstupní toky dat, se nazývají _šrouby_. Dělají skutečné filtrování dat, výběry a agregace.

SCP podporuje nejlepší úsilí, alespoň jednou a přesně jednou zpracování dat.

V aplikaci pro zpracování distribuovaného datového proudu může dojít k chybám během zpracování dat. Mezi takové chyby patří výpadek sítě, selhání počítače nebo chyba v kódu. Alespoň jednou zpracování zajišťuje, že všechna data jsou zpracována alespoň jednou automatickým přehráním stejných dat, když dojde k chybě.

Alespoň jednou zpracování je jednoduché a spolehlivé a vyhovuje mnoha aplikacím. Ale když aplikace vyžaduje přesné počítání, alespoň jednou zpracování je nedostatečná, protože stejná data mohou být přehrány v topologii aplikace. V takovém případě přesně po zpracování zajišťuje, že výsledek je správný i v případě, že jsou data přehrána a zpracována vícekrát.

SCP umožňuje vývojářům rozhraní .NET vytvářet aplikace pro zpracování dat v reálném čase při používání virtuálního počítače Java (JVM) s stormem. JVM a .NET komunikovat prostřednictvím místních soketů TCP. Každý výtok/šroub je dvojice procesů .NET/Java, kde se v procesu .NET jako modul plug-in spustí logika uživatele.

Chcete-li vytvořit aplikaci pro zpracování dat nad scp, postupujte takto:

1. Navrhněte a implementujte výtoky pro vytažení dat z front.
1. Navrhujte a implementujte šrouby, které zpracovávají vstupní data a ukládají je do externích úložišť, jako je databáze.
1. Navrhněte topologii, pak ji odešlete a spusťte.

Topologie definuje vrcholy a data, která mezi nimi proudí. SCP přebírá specifikaci topologie a nasazuje ji do clusteru Storm, kde každý vrchol běží na jednom logickém uzlu. Plánovač úloh bouře se postará o převzetí služeb při selhání a škálování.

Tento článek používá některé jednoduché příklady procházet, jak vytvářet aplikace pro zpracování dat s SCP.

## <a name="scp-plug-in-interface"></a>Rozhraní modulu plug-in SCP

Moduly plug-in SCP jsou samostatné aplikace. Mohou běžet uvnitř sady Visual Studio během vývoje a být připojen k kanálu Storm po nasazení v produkčním prostředí.

Zápis modulu plug-in SCP je stejný jako zápis jakékoli jiné konzolové aplikace systému Windows. Platforma SCP.NET deklaruje některá rozhraní pro výtok/šroub. Váš kód plug-in implementuje tato rozhraní. Hlavním účelem tohoto návrhu je umožnit vám soustředit se na obchodní logiku a zároveň nechat platformu SCP.NET zvládnout jiné věci.

Váš kód plug-in implementuje jedno z následujících rozhraní. Rozhraní, které závisí na tom, zda je topologie transakční nebo netransakční a zda je součástí výtok nebo šroub.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>Protokol ISCPPlugin

**ISCPPlugin** je společné rozhraní pro mnoho plug-inů. V současné době je to fiktivní rozhraní.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** je rozhraní pro netransakční hubici.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Při **NextTuple** je volána, váš kód C# může vyzařovat jeden nebo více řazených kolekcí členů. Pokud není nic vyzařovat, tato metoda by měla vrátit bez emitování nic.

**NextTuple**, **Ack**a **Fail** metody jsou všechny volány v těsné smyčky v jednom vlákně procesu Jazyka C#. Pokud neexistují žádné řazené kolekce členů k emitování, mají **NextTuple** spánku na krátkou dobu, jako je 10 milisekund. Tento spánek pomáhá vyhnout se plýtvání dostupnosti procesoru.

**Ack** a **Fail** metody jsou volány pouze v případě, že soubor specifikace umožňuje mechanismus potvrzení. Parametr *seqId* identifikuje řazenou kolekce členů, která je potvrzena nebo se nezdařila. Pokud je potvrzení povoleno v netransakční topologii, měla by být v hubici použita následující funkce **Emit:**

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Pokud netransakční topologie nepodporuje potvrzení, **Ack** a **Fail** může být ponechána jako prázdné funkce.

Vstupní parametr *parms* v těchto funkcích určuje prázdný slovník a je vyhrazen pro budoucí použití.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** je rozhraní pro netransakční šroub.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Je-li k dispozici nová řazená kolekce členů, je volána funkce **Execute,** která ji zpracovává.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** je rozhraní pro transakční hubici.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Stejně jako jejich netransakční protějšky, **NextTx**, **Ack**a **Fail** jsou všechny volány v těsné smyčce v jednom vlákně procesu Jazyka C#. Pokud neexistují žádné řazené kolekce členů k emitování, mají **NextTx** spánku na krátkou dobu, jako je 10 milisekund. Tento spánek pomáhá vyhnout se plýtvání dostupnosti procesoru.

Při **NextTx** je volána ke spuštění nové transakce, *seqId* výstupní parametr identifikuje transakce. Transakce se také používá v **Ack** a **Fail**. Vaše metoda **NextTx** může vyzařovat data na stranu Javy. Data jsou uložena v ZooKeeper pro podporu přehrávání. Protože ZooKeeper má omezenou kapacitu, váš kód by měl emitovat pouze metadata a ne hromadná data v transakční můře.

Vzhledem k tomu, že storm automaticky přehraje neúspěšnou transakci, **fail** obvykle nebude volána. Ale pokud SCP můžete zkontrolovat metadata vyzařovaná transakční výtok, může volat **Fail,** když metadata je neplatná.

Vstupní parametr *parms* v těchto funkcích určuje prázdný slovník a je vyhrazen pro budoucí použití.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** je rozhraní pro transakční šroub.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

**Execute** Metoda je volána při nové řazené kolekce členů dorazí na šroub. **FinishBatch** Metoda je volána po ukončení této transakce. Vstupní parametr *parms* je vyhrazen pro budoucí použití.

Pro transakční topologie **StormTxAttempt** je důležitá třída. Má dva členy: **TxId** a **AttemptId**. Člen **TxId** identifikuje konkrétní transakci. Transakce může být pokus vícekrát, pokud se nezdaří a je přehrán.

SCP.NET vytvoří nový objekt **ISCPBatchBolt** pro zpracování každého objektu **StormTxAttempt,** stejně jako storm dělá v Javě. Účelem tohoto návrhu je podpora paralelního zpracování transakcí. Po dokončení pokusu o transakci je odpovídající objekt **ISCPBatchBolt** zničen a uvolněno.

## <a name="object-model"></a>Objektový model

SCP.NET také poskytuje jednoduchou sadu klíčových objektů pro vývojáře programovat. Objekty jsou **Context**, **StateStore**a **SCPRuntime**. Jsou diskutovány v této sekci.

### <a name="context"></a>Kontext

**Objekt Context** poskytuje spuštěné prostředí pro aplikaci. Každá instance **ISCPPlugin** **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**nebo **ISCPBatchBolt** má odpovídající instanci **Context.** Funkce poskytované **context** je rozdělena do těchto dvou částí:

* Statický díl, který je k dispozici v celém procesu Jazyka C#.
* Dynamická část, která je k dispozici pouze pro konkrétní instanci **Context**

### <a name="static-part"></a>Statický díl

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

Logger **Logger** objekt je k dispozici pro účely protokolování.

Objekt **pluginType** označuje typ modulu plug-in procesu jazyka C#. Pokud je proces spuštěn v místním testovacím režimu bez jazyka Java, typ modulu plug-in je **SCP_NET_LOCAL**.

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

Vlastnost **Config** získá parametry konfigurace ze strany Java, která je předá při inicializování modulu plug-in plug-in C#. Parametry **Config** jsou rozděleny do dvou částí: **stormConf** a **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**StormConf** část je parametry definované Storm, a **pluginConf** část je parametry definované SCP. Tady je příklad:

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

Typ **TopologyContext** získá kontext topologie. Je to nejužitečnější pro více paralelních součástí. Tady je příklad:

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

Následující rozhraní se vztahuje k určité instanci **Context,** která je vytvořena platformou SCP.NET a předána vašemu kódu:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Pro netransakční výtok, který podporuje potvrzení, je k dispozici následující metoda:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Netransakční šroub, který podporuje potvrzení by měl explicitně volat **Ack** nebo **Fail** s n-tice, které obdržel. Při vyzařování nové řazené kolekce členů musí šroub také určit kotvy n-tice. Jsou uvedeny následující metody:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>Úložiště stavu

Objekt **StateStore** poskytuje služby metadat, generování monotónní sekvence a bezčekací koordinaci. Můžete vytvořit vyšší úrovně distribuované souběžnosti abstrakce na **StateStore**. Tyto abstrakce zahrnují distribuované zámky, distribuované fronty, bariéry a transakční služby.

SCP aplikace mohou použít **State** objekt serializovat informace v [Apache ZooKeeper](https://zookeeper.apache.org/). Tato schopnost je zvláště cenná pro transakční topologii. Pokud transakční výtok přestane reagovat a restartuje, **State** můžete načíst potřebné informace z ZooKeeper a restartovat potrubí.

Objekt **StateStore** má tyto hlavní metody:

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

State **State** Objekt má tyto základní metody:

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

Když **simpleMode** je nastavena na **hodnotu true**, **Commit** metoda odstraní odpovídající ZNode v ZooKeeper. Jinak metoda odstraní aktuální ZNode a přidá nový uzel\_v potvrzené path.

### <a name="scpruntime"></a>Doba scběhu

Třída **SCPRuntime** poskytuje následující dvě metody:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Metoda **Initialize** inicializuje prostředí runtime SCP. V této metodě proces Jazyka C# se připojí k straně Java získat parametry konfigurace a kontextu topologie.

Metoda **LaunchPlugin** spustí smyčku zpracování zpráv. V této smyčce modul plug-in Jazyka C# přijímá zprávy ze strany Javy. Tyto zprávy zahrnují řazené kolekce členů a řídicí signály. Modul plug-in pak zpracuje zprávy, například voláním metody rozhraní poskytované vaším kódem.

Vstupní parametr pro **LaunchPlugin** je delegát. Metoda může vrátit objekt, který implementuje rozhraní **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**nebo **ISCPBatchBolt.**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Pro **ISCPBatchBolt**, můžete získat **StormTxAttempt** objekt z *parms* parametr a použít jej k posouzení, zda pokus je přehraný pokus. Kontrola pokusu o přehrání se často provádí na šroubu potvrzení. Příklad HelloWorldTx dále v tomto článku ukazuje tuto kontrolu.

Moduly plug-in SCP lze obvykle spustit ve dvou režimech: místní testovací režim a běžný režim.

#### <a name="local-test-mode"></a>Místní testovací režim

V tomto režimu moduly plug-in SCP v kódu jazyka C# spustit uvnitř sady Visual Studio během fáze vývoje. V tomto režimu můžete použít rozhraní **ILocalContext.** Rozhraní poskytuje metody serializovat vyzařované řazené kolekce členů do místních souborů a jejich čtení zpět do paměti RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Běžný režim

V tomto režimu spustí proces Storm Java moduly plug-in SCP. Zde je příklad:

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

Specifikace topologie SCP je jazyk specifický pro doménu (DSL) pro popis a konfiguraci topologie SCP. Je založen na [Clojure DSL Storm](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) a je rozšířen o SCP.

Specifikace topologie můžete odeslat přímo do clusteru Storm k provedení pomocí příkazu **runSpec.**

SCP.NET přidal následující funkce pro definování transakčnítopologie:

| Nová funkce | Parametry | Popis |
| --- | --- | --- |
| **tx-topolopy** |*název topologie*<br />*mapa výtoku*<br />*mapa šroubů* |Definuje transakční topologii s názvem topologie, mapou definice výtoku a mapou definice šroubů. |
| **scp-tx-hubice** |*exec-name*<br />*args*<br />*Pole* |Definuje transakční výtok. Funkce spustí aplikaci, která je určena *exec-name* a používá *args*.<br /><br />Parametr *fields* určuje výstupní pole pro výtok. |
| **scp-tx-batch-bolt** |*exec-name*<br />*args*<br />*Pole* |Definuje transakční dávkový šroub. Funkce spustí aplikaci, která je určena *exec-name* a používá *args.*<br /><br />Parametr *fields* určuje výstupní pole šroubu. |
| **scp-tx-commit-bolt** |*exec-name*<br />*args*<br />*Pole* |Definuje transakční šroub potvrzení. Funkce spustí aplikaci, která je určena *exec-name* a používá *args*.<br /><br />Parametr *fields* určuje výstupní pole šroubu. |
| **nontx topologie** |*název topologie*<br />*mapa výtoku*<br />*mapa šroubů* |Definuje netransakční topologii s názvem topologie, mapou definice výtoku a mapou definice šroubů. |
| **scp-hubice** |*exec-name*<br />*args*<br />*Pole*<br />*Parametry* |Definuje netransakční výtok. Funkce spustí aplikaci, která je určena *exec-name* a používá *args*.<br /><br />Parametr *fields* určuje výstupní pole pro výtok.<br /><br />Parametr *parametry* je volitelný. Slouží k určení parametrů jako "nontransactional.ack.enabled". |
| **scp-šroub** |*exec-name*<br />*args*<br />*Pole*<br />*Parametry* |Definuje netransakční šroub. Funkce spustí aplikaci, která je určena *exec-name* a používá *args*.<br /><br />Parametr *pole* určuje výstupní pole šroubu.<br /><br />Parametr *parametry* je volitelný. Slouží k určení parametrů jako "nontransactional.ack.enabled". |

SCP.NET definuje následující klíčová slova:

| Klíčové slovo | Popis |
| --- | --- |
| **:jméno** |Název topologie |
| **:topologie** |Topologie využívající funkce v předchozí tabulce a vestavěné funkce |
| **:p** |Rovnoběžník pro každou hubici nebo šroub |
| **:config** |Jestli nakonfigurovat parametry nebo aktualizovat stávající parametry |
| **:Schéma** |Schéma datového proudu |

SCP.NET také definuje tyto často používané parametry:

| Parametr | Popis |
| --- | --- |
| "plugin.name" |Název souboru EXE modulu plug-in Jazyka C# |
| "plugin.args" |Argumenty modulu plug-in |
| "output.schema" |Výstupní schéma |
| "nontransactional.ack.enabled" |Zda je potvrzení povoleno pro netransakční topologii |

Příkaz **runSpec** je nasazen společně s bity. Zde je příkaz použití:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Parametr *resource-dir* je volitelný. Zadejte, když chcete připojit aplikaci C#. Zadaný adresář obsahuje aplikaci, závislosti a konfigurace.

Parametr *cesta pro třídy* je také volitelný. Určuje cestu třídy Java, pokud soubor specifikace obsahuje java hubici nebo šroub.

## <a name="miscellaneous-features"></a>Různé funkce

### <a name="input-and-output-schema-declarations"></a>Deklarace vstupního a výstupního schématu

Vaše procesy jazyka C# můžete vyzařovat řazené kolekce členů. Chcete-li tak učinit, platforma serializuje řazené kolekce členů do **bajtů[]** objekty a přenáší objekty na straně Java. Storm pak přenese tyto n-tic na cíle.

V následných komponent, procesy Jazyka C# přijímat n-tic zpět ze strany Java a převést je na původní typy platformy. Všechny tyto operace jsou skryty platformou.

Pro podporu serializace a deserializace, váš kód musí deklarovat schéma vstupu a výstupu. Schéma je definováno jako slovník. ID datového proudu je klíč slovníku. Hodnota klíče jsou typy sloupců. Komponenta může deklarovat více datových proudů.

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

K objektu **Context** je přidána následující funkce:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Vývojáři musí zajistit, aby vyzařované řazené kolekce členů dodržovat schéma definované pro datový proud. V opačném případě systém vyvolá výjimku za běhu.

### <a name="multistream-support"></a>Podpora pro Multistream

SCP umožňuje váš kód emitovat nebo přijímat z více různých datových proudů současně. Context **Context** Objekt odráží tuto podporu jako **emit** metoda volitelné id datového proudu parametr.

Byly přidány dvě metody v objektu SCP.NET **Context.** Vyzařují jeden nebo více n-tic do konkrétních datových proudů. Parametr *streamId* je řetězec. Jeho hodnota musí být stejná v kódu Jazyka C# a ve specifikaci definice topologie.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Vyzařování do neexistujícího datového proudu způsobuje výjimky za běhu.

### <a name="fields-grouping"></a>Seskupení polí

Vestavěná pole seskupující v Stormu nefungují správně v SCP.NET. Na straně proxy jazyka Java je datový typ všech polí ve skutečnosti **bajt[]**. Seskupení polí používá k seskupení kód hash objektu **bajtu[].** Kód hash je adresa tohoto objektu v paměti RAM. Takže seskupení bude špatné pro vícebajtové objekty, které sdílejí stejný obsah, ale ne stejnou adresu.

SCP.NET přidá vlastní metodu seskupení a k seskupení použije obsah objektu **bajt[].** V souboru specifikace syntaxe vypadá jako v tomto příkladu:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

V předchozím souboru specifikace:

* `scp-field-group`určuje, že seskupení je vlastní seskupení polí implementované scp.
* `:tx`nebo `:non-tx` určuje, zda je topologie transakční. Tyto informace potřebujete, protože počáteční index se liší mezi transakční a netransakční topologie.
* `[0,1]`určuje sadu hash ID polí, která začínají nulou.

### <a name="hybrid-topology"></a>Hybridní topologie

Nativní kód Bouře je napsán v Javě. SCP.NET vylepšilstorm, aby vám umožnil psát kód Jazyka C# pro zpracování obchodní logiky. Ale SCP.NET také podporuje hybridní topologie, které obsahují nejen C # hubice / šrouby, ale také Java hubice / šrouby.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Zadejte java hubici/šroub ve specifikačním souboru

Můžete použít **scp-hubice** a **scp-bolt** ve specifikačním souboru k určení java hubic a šroubů. Tady je příklad:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Zde `microsoft.scp.example.HybridTopology.Generator` je název třídy java výtoku.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Určení cesty třídy Java v příkazu runSpec

Pokud chcete odeslat topologii, která obsahuje java hubice nebo šrouby, nejprve je zkompilujte a vyhovte souborům JAR. Pak zadejte java classpath, který obsahuje jar soubory při odesílání topologie. Tady je příklad:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Zde `examples\HybridTopology\java\target\` je složka obsahující java hubici / šroub JAR soubor.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializace a deserializace mezi javou a C #

Součást SCP zahrnuje stranu Java a c# stranu. Chcete-li pracovat s nativníjava výtoky / šrouby, serializace a deserializace musí dojít mezi java straně a c# straně, jak je znázorněno v následujícím grafu:

![Diagram java komponenty odesílající do složky SCP, která pak odešle do jiné komponenty Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serializace na straně Java a deserializace na straně C#

Nejprve zadejte výchozí implementaci pro serializaci na straně Java a deserializace na straně C#.

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

Zadejte metodu deserializace na straně C# v kódu jazyka C#.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Pokud datový typ není příliš složitý, tato výchozí implementace by měla zpracovat většinu případů. Zde jsou případy, kdy můžete připojit vlastní implementaci:

* Datový typ je příliš složitý pro výchozí implementaci.
* Výkon výchozí implementace nesplňuje vaše požadavky.

Serializační rozhraní na straně Javy je definováno jako:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

Rozhraní pro deserializaci na straně C# je definováno jako:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serializace na straně C# a deserializace na straně Java

Zadejte metodu serializace na straně C# v kódu jazyka C#.

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

Zde `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` je název deserializátoru `"microsoft.scp.example.HybridTopology.Person"` a je cílovou třídou, na kterou jsou data deserializována.

Můžete také připojit vlastní implementaci c# serializátor a deserializátor Java.

Tento kód je rozhraní pro serializátor jazyka C#:

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

## <a name="scp-host-mode"></a>Hostitelský režim SCP

V tomto režimu můžete zkompilovat kód jako DLL a použít SCPHost.exe, jak je k dispozici SCP k odeslání topologie. Soubor specifikace vypadá jako tento kód:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Zde `"plugin.name"` je zadán `"SCPHost.exe"`jako , který je poskytován sadou SDK SDK SCP. SCPHost.exe přijímá tři parametry v následujícím pořadí:

1. Název dll, který `"HelloWorld.dll"` je v tomto příkladu.
1. Název třídy, `"Scp.App.HelloWorld.Generator"` který je v tomto příkladu.
1. Název veřejné statické metody, která může být vyvolána získat instanci **ISCPPlugin**.

V hostitelském režimu kompilujte kód jako dll pro vyvolání platformou SCP. Vzhledem k tomu, že platforma pak může získat plnou kontrolu nad celou logikou zpracování, doporučujeme odeslat topologii v hostitelském režimu SCP. To zjednodušuje vývojové prostředí. Přináší také větší flexibilitu a lepší zpětnou kompatibilitu pro pozdější verze.

## <a name="scp-programming-examples"></a>Příklady programování SCP

### <a name="helloworld"></a>Helloworld

Následující jednoduchý příklad HelloWorld ukazuje chuť SCP.NET. Používá netransakční topologii s výtokem nazývaným **generátor** a dva šrouby nazývané **splitter** a **počítadlo**. Výtok **generátoru** náhodně generuje věty a vyzařuje tyto věty do **splitteru**. Šroub **rozdělovače** rozdělí věty na slova a vyzařuje tato slova do **šroubu čítače.** Šroub **čítače** používá slovník pro záznam výskytu každého slova.

Tento příklad má dva soubory specifikace: HelloWorld.spec a HelloWorld\_EnableAck.spec. Kód Jazyka C# můžete zjistit, zda potvrzení `pluginConf` je povoleno získáním objektu ze strany Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Pokud je v výtoku povoleno potvrzení, slovník uloží řazené kolekce členů do mezipaměti, které nebyly potvrzeny. Pokud `Fail` je volána, je přehráno neúspěšné řazené kolekce členů.

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

Následující příklad HelloWorldTx ukazuje, jak implementovat transakční topologie. V příkladu je jedna hubice nazývaná **generátor**, dávkový šroub nazývaný **částečný počet**a šroub potvrzení nazývaný **součet počítání**. Příklad má také tři existující textové soubory: DataSource0.txt, DataSource1.txt a DataSource2.txt.

V každé transakci **generátor** výtok náhodně vybere dva soubory z existujících tří souborů a vyzařuje dva názvy souborů na **šroub s částečným počtem.** Šroub **s částečným počtem:**

1. Získá název souboru z přijaté řazené kolekce členů.
1. Otevře odpovídající soubor.
1. Spočítá počet slov v souboru.
1. Vysune počet slov do šroubu **součet odpočítávání.**

Šroub **součet výpočtu** shrnuje celkový počet.

Chcete-li dosáhnout přesně jednou sémantiku, **snížení součtu** potvrzení šroub musí posoudit, zda je přehrané transakce. V tomto příkladu má následující statickou členovou proměnnou:

```csharp
public static long lastCommittedTxId = -1; 
```

Když je vytvořena instance **ISCPBatchBolt,** získá `txAttempt` hodnotu objektu ze vstupních parametrů.

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

Když `FinishBatch` je `lastCommittedTxId` volána, je aktualizován, pokud není přehrané transakce.

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

### <a name="hybridtopology"></a>Hybridní topologie

Tato topologie obsahuje java hubici a šroub C#. Používá výchozí serializaci a implementaci deserializace poskytovanou platformou SCP. Podrobnosti o souboru specifikace naleznete\\v souboru HybridTopology.spec v příkladech složky HybridTopology. Viz také SubmitTopology.bat, jak určit Java classpath.

### <a name="scphostdemo"></a>SCPHostDemo

Tento příklad je v podstatě stejný jako HelloWorld. Jediným rozdílem je, že váš kód je kompilován jako DLL a topologie je odeslána pomocí SCPHost.exe. Podrobnější vysvětlení naleznete v části režimu hostitele Protokolu SCP.

## <a name="next-steps"></a>Další kroky

Příklady topologií Apache Storm vytvořených pomocí SCP najdete v následujících článcích:

* [Vývoj topologií Jazyka C# pro Apache Storm na HDInsightu pomocí Visual Studia](apache-storm-develop-csharp-visual-studio-topology.md)
* [Zpracovávat události z Centra událostí Azure pomocí Apache Storm na HDInsightu](apache-storm-develop-csharp-event-hub-topology.md)
* [Zpracování dat ze senzorů vozidel z centra událostí pomocí Apache Storm na HDInsightu](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrahujte, transformujte a načtěte (ETL) z Azure Event Hubs do Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
