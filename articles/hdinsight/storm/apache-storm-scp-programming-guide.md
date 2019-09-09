---
title: Průvodce programováním v SCP.NET pro zaplavení ve službě Azure HDInsight
description: Naučte se používat SCP.NET k vytvoření. Topologie nenáročného zaplavení pro použití s více operačními systémy v Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: b7bb26cd35daf67a3337907aded18e3302b19d81
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813880"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Průvodce programováním SCP pro Apache Storm ve službě Azure HDInsight

SCP je platforma pro sestavování v reálném čase, spolehlivém, konzistentním a vysoce výkonném aplikaci pro zpracování dat. Je postavená na [Apache Storm](https://storm.incubator.apache.org/) – systém zpracování datových proudů navržený komunitou OSS. Zaplavování je navržené pomocí Nathan Marz a otevřelo ho pomocí Twitteru. Využívá [Apache Zookeeper](https://zookeeper.apache.org/), jiný projekt Apache, který umožňuje vysoce spolehlivou distribuovanou koordinaci a správu stavu. 

V systému Windows není pouze port spojovacího bodu služby, ale také projekt přidal rozšíření a přizpůsobení ekosystému systému Windows. Mezi tato rozšíření patří prostředí pro vývojáře .NET a knihovny, přizpůsobení zahrnuje nasazení založené na systému Windows. 

Rozšíření a přizpůsobení se provádí takovým způsobem, že nepotřebujete rozvětvit projekty OSS a můžeme využít odvozené ekosystémy, které jsou postaveny nad sebou.

## <a name="processing-model"></a>Model zpracování
Data ve spojovacím bodu služby jsou modelována jako kontinuální streamování řazených kolekcí členů. Obvykle se řazené kolekce členů napřed do některé fronty a následně vybírají a transformují se pomocí obchodní logiky hostované v rámci topologie s více podsítěmi, takže výstup může být rozdělený jako řazené kolekce členů do jiného systému SCP nebo je možné ho považovat za úložiště, jako je distribuovaný systém souborů nebo databáze. Podobně jako SQL Server.

![Diagram dat s dořazením do fronty pro zpracování, který informační kanály ukládá do úložiště dat](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

V sestavení je topologie aplikace definována grafem výpočtu. Každý uzel v topologii obsahuje logiku zpracování a odkazy mezi uzly označují tok dat. Uzly pro vložení vstupních dat do topologie se nazývají _spoutů_, které lze použít k sesekvencování dat. Vstupní data se můžou nacházet v protokolech souborů, transakční databázi, čítači výkonu systému atd. Uzly, které mají vstupní i výstupní datové toky, se nazývají _šrouby_, což znamená skutečné filtrování a výběr dat a agregace.

Spojovací bod služby podporuje nejlepší úsilí, nejméně jednou a zpracování dat právě jednou. V aplikaci pro zpracování distribuovaného streamování může během zpracování dat dojít k různým chybám, jako je výpadek sítě, selhání počítače nebo chyba uživatelského kódu atd. Při nesprávném zpracování se všechna data zpracují aspoň jednou přehráním, když se automaticky přehrají stejná data, když dojde k chybě. Nejméně jeden proces zpracování je jednoduchý a spolehlivý a dobře vyhovuje spoustě aplikací. Nicméně pokud aplikace vyžaduje přesné počítání, nestačí zpracování nejméně jednou, protože stejná data by mohla být v topologii aplikace potenciálně přehrána. V takovém případě je zpracování právě jednou navrženo tak, aby bylo zajištěno, že výsledek je správný i v případě, že data mohou být znovu přehrána a zpracována vícekrát.

Spojovací bod služby umožňuje vývojářům rozhraní .NET vyvíjet aplikace zpracovávající data v reálném čase při využití prostředí Java Virtual Machine (JVM) se zachováním na základě vazeb. Rozhraní .NET a JVM komunikují prostřednictvím místních soketů TCP. V podstatě každý Spout/šroub je dvojicí procesů .NET/Java, kde je logika uživatele spouštěna v procesu .NET jako modul plug-in.

Pokud chcete vytvořit aplikaci pro zpracování dat v rámci spojovacího bodu služby, je potřeba provést několik kroků:

* Navrhněte a implementujte rozhraní Spoutů, které se bude načítat do dat z fronty.
* Navrhněte a implementujte šrouby pro zpracování vstupních dat a uložte data do externích úložišť, jako je třeba databáze.
* Navrhněte topologii a pak tuto topologii odešlete a spusťte. Topologie definuje vrcholy a datové toky mezi vrcholy. Spojovací bod služby převezme specifikaci topologie a nasadí ji do clusteru s více podmnožinami, kde každý vrchol běží na jednom logickém uzlu. Převzetí služeb při selhání a škálování bude mít na starosti Plánovač úloh.

Tento dokument používá několik jednoduchých příkladů, které vám pomůžou při sestavování aplikace pro zpracování dat pomocí spojovacího bodu služby.

## <a name="scp-plugin-interface"></a>Rozhraní Plug-in spojovacího bodu služby
Moduly plug-in SCP (nebo aplikace) jsou samostatné exe, které je možné spustit uvnitř sady Visual Studio během vývojové fáze a připojit se k kanálu po nasazení v produkčním prostředí. Zápis modulu plug-in SCP je stejný jako při psaní jakýchkoli jiných standardních konzolových aplikací pro Windows. Platforma SCP.NET deklaruje některé rozhraní pro Spout/šroub a kód modulu plug-in uživatele by měl implementovat tato rozhraní. Hlavním účelem tohoto návrhu je, že se uživatel může soustředit na své vlastní obchodní logiky a nechat jiné věci, které mají být zpracovány platformou SCP.NET.

Kód modulu plug-in uživatele by měl implementovat jedno z následujících rozhraní, záleží na tom, jestli je topologie transakční, nebo netransakční, a jestli je součástí Spout nebo šroub.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin je společné rozhraní pro všechny druhy modulů plug-in. V současné době se jedná o fiktivní rozhraní.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout je rozhraní pro netransakční Spout.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Při `NextTuple()` volání metody může uživatelský kód\# C vygenerovat jednu nebo více řazených kolekcí členů. Pokud není nic k vygenerování, tato metoda by měla vracet bez nutnosti vysílat nic. Je nutné poznamenat, `NextTuple()`že `Ack()`, a `Fail()` jsou volány v těsné smyčce v jednom vlákně v procesu\# jazyka C. Pokud neexistují žádné řazené kolekce členů k vygenerování, je Courteous NextTuple režimu spánku po krátkou dobu (například 10 milisekund), aby nedocházelo k příliš velkému množství PROCESORů.

`Ack()`a `Fail()` jsou volány pouze v případě, že je v souboru spec povolen mechanismus ACK. `seqId` Slouží k identifikaci řazené kolekce členů, která je potvrzena nebo se nezdařila. Takže pokud je možnost ACK povolena v jiné než transakční topologii, měla by být v Spout použita následující funkce Emit:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Pokud se ACK nepodporuje v jiné než transakční topologii, `Ack()` může být a funkce a `Fail()` může být ponechána jako prázdná.

`parms` Vstupní parametr v těchto funkcích je prázdný slovník, který je vyhrazen pro budoucí použití.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt je rozhraní pro netransakčního šroubu.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Je-li k dispozici nová `Execute()` řazená kolekce členů, je volána funkce pro její zpracování.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout je rozhraní transakčního Spout.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Stejně jako `NextTx()`jejich netransakční součást,, `Ack()` `Fail()` a jsou všechny volány v těsné smyčce v jednom vlákně v procesu C\# . Pokud neexistují žádná data, která by se dala vysílat `NextTx` , je Courteous, aby se v režimu spánku po krátkou dobu (10 milisekund), takže neodpade příliš mnoho CPU.

`NextTx()`je volána pro spuštění nové transakce, parametr `seqId` out slouží k identifikaci transakce, která je také použita v `Ack()` a `Fail()`. V `NextTx()`nástroji může uživatel vysílat data na stranu Java. Data se ukládají do ZooKeeper pro podporu opětovného přehrání. Vzhledem k tomu, že kapacita ZooKeeper je omezená, by měl uživatel generovat jenom metadata, nikoli Hromadná data v transakčních Spout.

Zaplavení znovu spustí transakci automaticky, pokud selže, takže `Fail()` by neměl být volán v normálním případě. Pokud spojovací bod služby může ale kontrolovat metadata generovaná transakčním Spout, může zavolat `Fail()` , když metadata nejsou platná.

`parms` Vstupní parametr v těchto funkcích je prázdný slovník, který je vyhrazen pro budoucí použití.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt je rozhraní pro transakční šroub.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()`se volá, když se dorazí na šroub nové řazené kolekce členů. `FinishBatch()`se volá, když se tato transakce ukončí. `parms` Vstupní parametr je vyhrazen pro budoucí použití.

V případě transakční topologie existuje důležitý koncept – `StormTxAttempt`. Má dvě pole `TxId` a `AttemptId`. `TxId`slouží k identifikaci konkrétní transakce a pro danou transakci, může dojít k několika pokusům o více pokusů, pokud transakce selhává a je znovu přehrána. SCP.NET vytvoří nový objekt ISCPBatchBolt ke zpracování každého `StormTxAttempt`, stejně jako v jazyce Java. Účelem tohoto návrhu je podpora zpracování paralelních transakcí. Uživatel by měl mít na paměti, že pokud je pokus o transakci dokončený, je odpovídající objekt ISCPBatchBolt zničený a je uvolněný z paměti.

## <a name="object-model"></a>Objektový model
SCP.NET také poskytuje jednoduchou sadu klíčových objektů, pomocí kterých můžou vývojáři programovat. Jsou to **kontextové**, **úložiště stavu SMP**a **SCPRuntime**. Jsou popsány v části zbytek této části.

### <a name="context"></a>Kontext
Kontext nabízí běžící prostředí pro aplikaci. Každá instance ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) má odpovídající kontextovou instanci. Funkčnost poskytovaná kontextem může být rozdělena do dvou částí: (1) statická část, která je k dispozici v celém procesu\# jazyka C, (2) dynamické součásti, která je k dispozici pouze pro konkrétní instanci kontextu.

### <a name="static-part"></a>Statická část
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger`je k dispozici pro účely protokolování.

`pluginType`slouží k označení typu modulu plug-in v procesu\# jazyka C. Pokud je proces\# jazyka C spuštěn v místním testovacím režimu (bez Java), je `SCP_NET_LOCAL`typ modulu plug-in.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config`je k dispozici pro získání parametrů konfigurace z boku Java. Parametry jsou předány ze strany Java při inicializaci\# modulu plug-in jazyka C. Parametry jsou rozděleny do dvou částí: `stormConf` a `pluginConf`. `Config`

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`jsou parametry definované pomocí parametru, `pluginConf` který je definován pomocí spojovacího bodu služby. Příklad:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext`je k dispozici pro získání kontextu topologie, je nejužitečnější pro komponenty s více paralelismu. Zde naleznete příklad:

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

### <a name="dynamic-part"></a>Dynamická část
Následující rozhraní jsou relevantní pro určitou instanci kontextu. Instance kontextu je vytvořena platformou SCP.NET a předána do uživatelského kódu:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

V případě netransakčního spoutu, který podporuje potvrzení, je k dispozici Tato metoda:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

U netransakčního šroubu podporujícího potvrzení by se `Ack()` mělo `Fail()` explicitně nebo řazená kolekce členů přijmout. A při generování nové řazené kolekce členů musí také určovat kotvy nové řazené kolekce členů. K dispozici jsou následující metody.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>Úložiště stavu SMP
`StateStore`poskytuje služby metadat, generování sekvencí monotónní a koordinaci bez čekání. Distribuované abstrakce souběžnosti vyšší úrovně můžou být postavené na `StateStore`, včetně distribuovaných zámků, distribuovaných front, bariér a transakčních služeb.

Aplikace spojovacího bodu služby `State` mohou použít objekt k uchování některých informací v [Apache Zookeeper](https://zookeeper.apache.org/), zejména pro transakční topologii. V případě, že dojde k selhání transakčního Spout a restartování, může načíst potřebné informace z ZooKeeper a restartovat kanál.

`StateStore` Objekt hlavně má tyto metody:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

`State` Objekt hlavně má tyto metody:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

`Commit()` Pro metodu, pokud je simpleMode nastaveno na hodnotu true, odstraní odpovídající ZNode v Zookeeper. V opačném případě odstraní aktuální ZNode a do svěřené\_cesty přidá nový uzel.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime poskytuje následující dvě metody:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()`slouží k inicializaci běhového prostředí spojovacího bodu služby. V této metodě se proces C\# připojí k části Java a získá parametry konfigurace a kontext topologie.

`LaunchPlugin()`slouží k ukončení smyčky zpracování zpráv. V této smyčce modul plug-\# in jazyka C přijímá zprávy ve formě na straně Java (včetně řazených kolekcí členů a řídicích signálů) a pak zpracovává zprávy, třeba volání metody rozhraní, kterou poskytuje uživatelský kód. Vstupní parametr metody `LaunchPlugin()` je delegát, který může vracet objekt, který implementuje rozhraní ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Pro ISCPBatchBolt můžeme získat `StormTxAttempt` z `parms`a použít ho k posouzení, zda se jedná o povedený pokus o přehrání. Pokus o opakované přehrání se často provádí na poli potvrzení a je znázorněno v `HelloWorldTx` příkladu.

Moduly plug-in SCP můžou být běžně spuštěné ve dvou režimech:

1. Místní testovací režim: V tomto režimu se moduly plug-in SCP (\# kód uživatele jazyka C) spouštějí v rámci sady Visual Studio během fáze vývoje. `LocalContext`dá se použít v tomto režimu, který poskytuje metodu pro serializaci vygenerovaných řazených kolekcí členů do místních souborů a jejich čtení zpátky do paměti.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Normální režim: V tomto režimu se moduly plug-in SCP spustí procesem zaplavení Java.
   
    Tady je příklad spuštění modulu plug-in SCP:
   
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

## <a name="topology-specification-language"></a>Jazyk specifikace topologie
Specifikace topologie spojovacího bodu služby je jazyk specifický pro doménu, který popisuje a konfiguruje topologie SCP. Vychází z Clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) a je rozšířeno pomocí spojovacího bodu služby.

Specifikace topologie je možné odeslat přímo do clusteru nečinnosti, aby je bylo možné spustit pomocí příkazu ***runspec*** .

SCP.NET přidal následující funkce pro definování transakčních topologií:

| **Nové funkce** | **Parametry** | **Popis** |
| --- | --- | --- |
| **TX – topolopy** |název topologie<br />Spout – mapa<br />Mapa šroubů |Definice transakční topologie s názvem topologie, &nbsp;mapou definice spoutů a mapou definice šrouby |
| **scp-tx-spout** |Exec – název<br />args<br />pole |Definujte transakční Spout. Spustí aplikaci s ***názvem Exec-Name*** pomocí ***argumentů***.<br /><br />***Pole*** jsou výstupní pole pro Spout |
| **SCP – TX-Batch-šroub** |Exec – název<br />args<br />pole |Definujte transakčního dávkovacího šroubu. Spustí aplikaci s ***názvem Exec-Name*** pomocí ***argumentů.***<br /><br />Pole jsou výstupní pole pro šroub. |
| **scp-tx-commit-bolt** |Exec – název<br />args<br />pole |Definujte hodnotu transakčního potvrzení. Spustí aplikaci s ***názvem Exec-Name*** pomocí ***argumentů***.<br /><br />***Pole*** jsou výstupní pole pro šroub |
| **nontx-topolopy** |název topologie<br />Spout – mapa<br />Mapa šroubů |Definice netransakční topologie s názvem topologie,&nbsp; mapou definice spoutů a mapou definice šrouby |
| **scp-spout** |Exec – název<br />args<br />pole<br />parameters |Definujte netransakční Spout. Spustí aplikaci s ***názvem Exec-Name*** pomocí ***argumentů***.<br /><br />***Pole*** jsou výstupní pole pro Spout<br /><br />***Parametry*** jsou volitelné a používají je k určení některých parametrů, například "netransakční transakce. ACK. Enabled". |
| **scp-bolt** |Exec – název<br />args<br />pole<br />parameters |Definování netransakčního šroubu Spustí aplikaci s ***názvem Exec-Name*** pomocí ***argumentů***.<br /><br />***Pole*** jsou výstupní pole pro šroub<br /><br />***Parametry*** jsou volitelné a používají je k určení některých parametrů, například "netransakční transakce. ACK. Enabled". |

SCP.NET má definovaná následující klíčová slova:

| **klíčová slova** | **Popis** |
| --- | --- |
| **: název** |Zadejte název topologie. |
| **: topologie** |Definování topologie pomocí předchozích funkcí a sestavení v nich. |
| **:p** |Definujte pomocný parametr paralelismus pro každý Spout nebo šroub. |
| **: config** |Definovat parametr konfigurace nebo aktualizovat existující |
| **:schema** |Definujte schéma streamu. |

A často používané parametry:

| **Parametr** | **Popis** |
| --- | --- |
| **"plugin.name"** |název souboru exe C# modulu plug-in |
| **"plugin.args"** |argumenty modulu plug-in |
| **"output.schema"** |Výstupní schéma |
| **"netransakční. ACK. Enabled"** |Zda je povoleno potvrzení pro netransakční topologii |

Příkaz runspec je nasazený společně s bity, použití je například:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Parametr ***Resource-dir*** je nepovinný, je nutné ho zadat, pokud chcete připojit aplikaci jazyka C\# a tento adresář obsahuje aplikaci, závislosti a konfigurace.

Parametr ***classpath*** je také volitelný. Používá se k určení cesty třídy Java, pokud soubor specifikace obsahuje Java Spout nebo šroub.

## <a name="miscellaneous-features"></a>Různé funkce
### <a name="input-and-output-schema-declaration"></a>Vstupní a výstupní deklarace schématu
Uživatelé mohou nasílat řazené\# kolekce členů v procesech jazyka C, platforma musí serializovat řazenou kolekci členů na Byte [], přenést na stranu Java a tato řazená kolekce členů převede do cílů. \# Procesy v rámci navazujících součástí dostanou řazené kolekce členů zpátky ze strany Java a převádějí je na původní typy podle platformy. všechny tyto operace jsou pro platformu skryté.

Aby bylo možné podporovat serializaci a deserializaci, musí kód uživatele deklarovat schéma vstupů a výstupů.

Schéma vstupního/výstupního datového proudu je definováno jako slovník. Klíčem je StreamId. Hodnota je typy sloupců. Komponenta může mít deklarované více datových proudů.

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


V objektu Context máme přidané toto rozhraní API:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Vývojáři musí zajistit, aby se řazené kolekce členů řídily podle schématu definovaného pro tento datový proud, jinak systém vyvolá výjimku za běhu.

### <a name="multi-stream-support"></a>Podpora více proudů
Spojovací bod služby podporuje uživatelský kód pro vygenerování nebo příjem z více různých datových proudů současně. Podpora odráží kontextový objekt, protože metoda Emit přebírá volitelný parametr ID streamu.

Byly přidány dvě metody v objektu kontextu SCP.NET. Slouží k vygenerování řazené kolekce členů nebo řazené kolekce členů k určení StreamId. StreamId je řetězec, který musí být konzistentní jak v jazyce C\# , tak ve specifikaci definice topologie.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Vygenerování do neexistujícího datového proudu způsobí výjimky za běhu.

### <a name="fields-grouping"></a>Seskupení polí
Vestavěná pole seskupování v rámci přetvoření nefungují správně v SCP.NET. Na straně proxy Java jsou všechna pole datových typů skutečně Byte [] a seskupení polí používá k provedení seskupení kód hodnoty hash objektu Byte []. Bajtový kód hodnoty hash objektu je adresa tohoto objektu v paměti. Takže seskupení bude chybné pro dva bajty objektů, které sdílejí stejný obsah, ale ne stejnou adresu.

SCP.NET přidá přizpůsobenou metodu seskupení a pomocí obsahu Byte [] provede seskupení. V souboru **spec** je syntaxe stejná jako:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Tady

1. "SCP-Field-Group" znamená "přizpůsobené seskupení polí implementované spojovacím bod služby".
2. ": TX" nebo ": non-TX" znamená, že je transakční topologie. Tyto informace potřebujeme, protože počáteční index se liší v topologiích TX vs. non-TX.
3. [0, 1] znamená sadu hodnot hash ID polí od 0.

### <a name="hybrid-topology"></a>Hybridní topologie
Nativní přepisování je napsané v jazyce Java. A SCP.NET je rozšířila, aby umožnila vývojářům v\# jazyce c\# psát kód jazyka c pro zpracování obchodní logiky. Podporuje ale také hybridní topologie, které neobsahují jenom C\# spoutů/šrouby, ale také Java Spout/šrouby.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Zadat Java Spout/šroub v souboru spec
V souboru spec se dá také použít spojovací bod služby (SCP-Spout) a spojovací bod služby (SCP-šroub) k určení Java Spoutů a šrouby, tady je příklad:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Tady `microsoft.scp.example.HybridTopology.Generator` je název třídy Java Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Určení cesty tříd Java v příkazu runSpec
Pokud chcete odeslat topologii obsahující Java Spoutů nebo šrouby, musíte nejdřív zkompilovat Java Spoutů nebo šrouby a získat soubory jar. Pak byste měli zadat cestu třídy Java, která obsahuje soubory jar při odesílání topologie. Zde naleznete příklad:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Tady **jsou\\příklady\\HybridTopologyJava\\target\\**  je složka obsahující soubor JAR Spout/šroubu Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializace a deserializace mezi Java a C\#
Komponenta SCP zahrnuje stranu Java a stranu\# C. Aby bylo možné pracovat s nativním Java spoutů/šrouby, je třeba provést serializaci nebo deserializaci mezi stranou Java a\# stranou C, jak je znázorněno v následujícím grafu.

![Diagram součástí, které se odesílají do komponenty SCP odeslání do komponenty jazyka Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serializace na straně jazyka Java a deserializace\# v oblasti C**
   
   Nejprve poskytujeme výchozí implementaci serializace na straně Java a deserializaci v oblasti\# C. Metodu serializace na straně Java lze zadat v souboru specifikace:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Metoda deserializace na straně c\# by měla být specifikována v\# uživatelském kódu jazyka c:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Tato výchozí implementace by měla zpracovat většinu případů za předpokladu, že datový typ není příliš složitý. V některých případech buď vzhledem k tomu, že datový typ uživatele je příliš složitý, nebo protože výkon naší výchozí implementace nesplňuje požadavky uživatele, uživatelé můžou vlastní implementaci připojit.
   
   Rozhraní serializace na straně Java je definováno jako:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   Deserializovat rozhraní na straně C\# je definováno jako:
   
   veřejné rozhraní ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serializace na\# straně C a deserializace v jazyce Java**
   
   Metoda serializace na straně\# c by měla být specifikována\# v uživatelském kódu jazyka c:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Metoda deserializace na straně Java by měla být specifikována v souboru SPEC:
   
     (SCP – Spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Tady je "Microsoft. SCP. CustomizedInteropJSONDeserializer. getlang." je název deserializátoru a "Microsoft. SCP. example. HybridTopology. Person" je cílová třída, na kterou se data deserializovat.
   
   Uživatel může také připojit svou vlastní implementaci serializátoru jazyka\# C a deserializaci Java. Tento kód je rozhraní serializátoru jazyka\# C:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Tento kód je rozhraní pro deserializaci jazyka Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP Host Mode
V tomto režimu může uživatel zkompilovat své kódy do knihovny DLL a pomocí SCPHost. exe, který poskytuje spojovací bod služby (SCP) k odeslání topologie. Soubor specifikace vypadá jako tento kód:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Tady je `plugin.name` zadaný, jak `SCPHost.exe` poskytuje sada SCP SDK. SCPHost. exe přijímá tři parametry:

1. První z nich je název knihovny DLL, který je `"HelloWorld.dll"` v tomto příkladu.
2. Druhá je název třídy, který je `"Scp.App.HelloWorld.Generator"` v tomto příkladu.
3. Třetí z nich je název veřejné statické metody, kterou lze vyvolat pro získání instance ISCPPlugin.

V režimu hostitele je kód uživatele kompilován jako knihovna DLL a je vyvoláno platformou SCP. Proto platforma SCP může získat úplnou kontrolu nad celou logikou zpracování. Proto doporučujeme, aby naši zákazníci mohli odeslat topologii v hostitelském režimu spojovacího bodu služby, protože můžou zjednodušit vývojové prostředí a zvýšit flexibilitu a lepší zpětnou kompatibilitu pro pozdější vydání.

## <a name="scp-programming-examples"></a>Příklady programování SCP
### <a name="helloworld"></a>Hell
**HelloWorld** je jednoduchý příklad pro zobrazení vkusu SCP.NET. Používá netransakční topologii s Spout nazvaný **generátor**a má dvě šrouby označované jako **rozdělovač** a **čítač**. **Generátor** Spout náhodně generuje věty a vygeneruje tyto věty do **rozdělovače**. **Rozdělovač** šroub rozdělí věty na slova a vygeneruje tato slova na **čítač** . Hodnota šroubu používá slovník k záznamu počtu výskytů každého slova.

V tomto příkladu jsou dva soubory specifikace: **HelloWorld. spec** a **\_HelloWorld EnableAck. spec** . V kódu jazyka\# C může zjistit, zda je povoleno potvrzení, získáním pluginConf ze strany Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Pokud je v Spout povolená možnost ACK, použije se slovník k ukládání řazených kolekcí členů, které nebyly potvrzeny. Pokud je volána metoda Fail (), dojde k neúspěšnému přehrání řazené kolekce členů:

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

### <a name="helloworldtx"></a>HelloWorldTx
Příklad **HelloWorldTx** ukazuje, jak implementovat transakční topologii. Má jeden Spout nazvaný **generátor**, dávkovou hodnotu s názvem **částečný počet**a potvrzovací šroub s názvem **Count-suma**. K dispozici jsou také tři předem vytvořené soubory TXT: **DataSource0. txt**, **DataSource1. txt**a **DataSource2. txt**.

V každé transakci **generátor** Spout náhodně vybere dva soubory z předem vytvořených tří souborů a vygeneruje tyto dva názvy souborů do šroubů s **částečným počtem** . **Částečný počet** šroubů Získá název souboru z přijaté řazené kolekce členů, pak otevře soubor a spočítá počet slov v tomto souboru a nakonec vygeneruje slovo číslo do pole **Count-suma** . Hodnota **čítače Count-suma** shrnuje celkový počet.

Chcete-li dosáhnout **přesně jedné** sémantiky, je nutné, aby **součet hodnot** šroubů byl posuzovat, zda se jedná o přehrajte transakci. V tomto příkladu má statickou členskou proměnnou:

    public static long lastCommittedTxId = -1; 

Když je vytvořena instance ISCPBatchBolt, získá `txAttempt` ze vstupních parametrů:

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

Když `FinishBatch()` je volána `lastCommittedTxId` , bude aktualizována, pokud se nejedná o převedenou transakci.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
Tato topologie obsahuje Java Spout a šroub v jazyce\# C. Používá výchozí serializaci a deserializaci, která je poskytována platformou SCP. V tématu **HybridTopology. spec** v **\\příkladech HybridTopology** složky najdete podrobnosti souboru specifikace a **SubmitTopology. bat** , jak zadat cestu třídy jazyka Java.

### <a name="scphostdemo"></a>SCPHostDemo
Tento příklad je stejný jako HelloWorld v podstatě. Jediným rozdílem je, že kód uživatele je kompilován jako knihovna DLL a topologie je odeslána pomocí SCPHost. exe. Podrobnější vysvětlení najdete v části "režim hostitele spojovacího bodu služby".

## <a name="next-steps"></a>Další kroky
Příklady topologií Apache Storm vytvořených pomocí spojovacího bodu služby (SCP) najdete v následujících dokumentech:

* [Vývoj C# topologií pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Zpracování událostí z Azure Event Hubs s využitím Apache Storm ve službě HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Zpracování dat snímače vozidla z Event Hubs pomocí Apache Storm v HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrakce, transformace a načítání (ETL) z Azure Event Hubs do Apache HBA](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
