---
title: Průvodce programováním pro SCP.NET pro Storm v Azure HDInsight
description: Další informace o použití SCP.NET vytvořit. Na základě NET topologií Storm pro použití se Stormem spuštěné v Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: 9b3fc80d129a42e68e877f4d1210e3ab10e0664a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631817"
---
# <a name="scp-programming-guide"></a>Průvodce programováním pro spojovací bod služby
Spojovací bod služby je platformou můžete tvořit v reálném čase spolehlivé a konzistentní a zpracování dat vysoce výkonné aplikace. Systém orchard je založen na horní [Apache Storm](https://storm.incubator.apache.org/) – systém ve komunity OSS pro zpracování datových proudů. Storm je určen Nathan marz a byla open source služba Twitter. Využívá [Apache ZooKeeper](https://zookeeper.apache.org/), jiný projekt Apache umožňující vysoce spolehlivých distribuovaných správy koordinace a stavu. 

Pouze projekt spojovací bod služby přenést Storm ve Windows, ale také projekt přidán rozšíření a přizpůsobení pro ekosystém Windows. Rozšíření patří prostředí pro vývojáře .NET a knihovny, přizpůsobení zahrnuje nasazení založené na Windows. 

Rozšíření a přizpůsobení se provádí tak, že jsme není nutné vytvořit fork projekty OSS a abychom mohli využívat odvozené ekosystémů postavené na Storm.

## <a name="processing-model"></a>Zpracování modelu
Data v spojovací bod služby je modelovaná jako nepřetržité proudy řazených kolekcí členů. Obvykle řazené kolekce členů tok do některé fronty nejprve pak vyzvedávání a transformovat obchodní logiky, které hostují uvnitř topologie Storm, nakonec výstup může rourou jako řazené kolekce členů do jiného systému spojovací bod služby, nebo se zaměřuje na úložiště, jako je distribuovaný systém souborů nebo databáze jako je SQL Server.

![Diagram fronty tak data pro zpracování, které se předají úložiště dat](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Storm definuje výpočetní graf, topologie aplikací. Každý uzel v topologii obsahuje logiku zpracování a vazby mezi uzly označení datového toku. Uzly se vložit vstupní data do topologie se nazývají _spouts_, který slouží k sekvencování data. Vstupní data může nacházet v souboru protokolů, transakční databází, čítače výkonu systému atd. Uzly s toky oba vstupní a výstupní data se nazývají _bolts_, který proveďte filtrování skutečná data a výběry a agregaci.

Spojovací bod služby podporuje maximální úsilí na alespoň jedno a přesně-jednou data zpracování. Do aplikace distribuované zpracování datových proudů různých chybám může dojít během zpracování dat, jako je výpadek sítě, selhání počítače nebo Chyba kódu uživatele atd. Na alespoň jedno zpracování zajistí, že všechna data se zpracuje alespoň jednou po přehrání automaticky stejná data, když dochází k chybě. V alespoň jedno zpracování je jednoduché a spolehlivé a vyhovuje také mnoho aplikací. Pokud aplikace vyžaduje přesně počítání, na alespoň jedno zpracování je však nedostatečné od stejných dat může potenciálně možné přehrát v topologii aplikace. V takovém, přesně-po zpracování slouží k Ujistěte se, že výsledek je správný, i když data mohou být znovu přehrát a zpracování více než jednou.

Spojovací bod služby umožňuje vývojářům .NET k vývoji aplikací procesu reálném čase data přitom můžete využívat na jazyce Java Virtual Machine (JVM) pomocí Stormu na pozadí. .NET a JVM komunikovat přes místní sockety TCP. V podstatě každý Spout/Bolt je pár .net nebo Javě procesu, kde logiku uživatelského běží v procesu .net jako modul plug-in.

Jak vytvořit aplikaci zpracování dat nad rámec spojovací bod služby, jsou potřeba několik kroků:

* Navrhujte a implementujte Spoutů, aby se načetla data z fronty.
* Návrh a implementaci funkce Bolts ke zpracování vstupních dat a ukládání dat do externího úložiště, například do databáze.
* Návrh topologie, Odeslat a spustit topologii. Topologie definuje vrcholů a data toky mezi vrcholů. Spojovací bod služby bude trvat specifikaci topologie a nasadit virtuální počítač na cluster Storm, kde každý vrchol běží na jednom uzlu logické. Převzetí služeb při selhání a škálování se postarat Storm Plánovač úloh.

Tento dokument vás provede postupem vytvořte aplikaci pro zpracování dat pomocí spojovacího bodu služby pomocí několik jednoduchých příkladů.

## <a name="scp-plugin-interface"></a>Rozhraní modulu plug-in spojovací bod služby
Samostatné souborů EXE, které oba a můžete spouštět v sadě Visual Studio během vývojové fáze, se zapojí se do kanálu Storm po nasazení v produkčním prostředí jsou moduly plug-in spojovací bod služby (a aplikace). Psaní modulu plug-in spojovací bod služby je stejně jako všechny ostatní standardní Windows konzolové aplikace psaní. Platforma SCP.NET deklaruje několik rozhraní pro funkcí spout/bolt a uživatelský kód modulu plug-in by měla tato rozhraní implementují. Hlavním účelem tohoto návrhu je, že uživatel zaměřit na své vlastní obchodní logics a opuštění jiné zpracovávat SCP.NET platformy.

Modul plug-in kód uživatele musí implementovat jedno z těchto hodnot rozhraní, závisí na tom, jestli topologie transakční či netransakční a určuje, zda je součást funkcích spout nebo bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin je společné rozhraní pro všechny druhy moduly plug-in. V současné době je fiktivní rozhraní.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout je rozhraní pro netransakční spout.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Když `NextTuple()` je volána z jazyka C\# kód uživatele může vysílat jeden nebo více řazené kolekce členů. Pokud není nic ke generování, tato metoda by měla vrátit bez generování cokoli. Je třeba poznamenat, že `NextTuple()`, `Ack()`, a `Fail()` jsou volány v těsné smyčce v jednom vlákně v jazyce C\# procesu. Pokud neexistují žádné řazených kolekcí členů a vygenerovat, je zdvořilý mít z režimu spánku NextTuple krátkém čase (například 10 milisekund) tak, aby odpad příliš mnoho procesoru.

`Ack()` a `Fail()` jsou volány pouze při potvrzení mechanismus je povolené v specifikace souboru. `seqId` Slouží k identifikaci řazené kolekce členů, které je potvrzeno nebo se nezdařilo. Takže pokud je potvrzení je povoleno v netransakční topologii, byste měli použít následující funkce vygeneruje ve Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Pokud ack není podporován v netransakční topologii, `Ack()` a `Fail()` může být ponecháno prázdné funkce.

`parms` Vstupní parametr v těchto funkcí je prázdný slovník, je vyhrazený pro budoucí použití.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt je rozhraní pro netransakční bolt.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Pokud nový řazené kolekce členů je k dispozici, `Execute()` voláním funkce se ji zpracovat.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout je rozhraní pro transakční spout.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Stejně jako jejich netransakční čítače část `NextTx()`, `Ack()`, a `Fail()` jsou volány v těsné smyčce v jednom vlákně v jazyce C\# procesu. Pokud nejsou žádná data a vygenerovat, je zdvořilý mít `NextTx` krátkého času (10 milisekund) tak, aby odpad příliš mnoho využití procesoru v režimu spánku.

`NextTx()` volá se, že spustit novou transakci, výstupní parametr `seqId` slouží k identifikaci transakce, která se používá také v `Ack()` a `Fail()`. V `NextTx()`, uživatele může vysílat dat na straně Java. Jsou data uložená v ZooKeeper pro podporu opětovného přehrání. Vzhledem k tomu, že je omezená kapacita ZooKeeper, uživatel by měl pouze poskytl metadata, není hromadné dat v transakční spout.

Bude Storm přehrát transakce automaticky, pokud selže, tak `Fail()` by neměla být volána v normální. Pokud spojovací bod služby můžete zkontrolovat, protože ho vygeneroval transakční spout metadata, můžete volat, ale `Fail()` při metadat je neplatný.

`parms` Vstupní parametr v těchto funkcí je prázdný slovník, je vyhrazený pro budoucí použití.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt je rozhraní pro transakční bolt.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` je volána, když je nový řazené kolekce členů přicházejících u bolt. `FinishBatch()` je volána, když je tato transakce skončila. `parms` Vstupní parametr je vyhrazená pro budoucí použití.

Pro transakční topologii je důležitý koncept – `StormTxAttempt`. Má dvě pole, `TxId` a `AttemptId`. `TxId` slouží k identifikaci konkrétní transakcí a pro dané transakce, může mít více pokusů transakce selže a je znovu přehrát. SCP.NET vytvoří nový objekt ISCPBatchBolt ke zpracování jednotlivých `StormTxAttempt`, stejně jako co dělá Storm v jazyce Java. Účelem tohoto návrhu je podporu paralelní zpracování transakcí. Uživatel byste jej zachovat v paměti, že pokud se pokus o transakci skončí, odpovídající objekt ISCPBatchBolt zničen a uvolněna z paměti.

## <a name="object-model"></a>Objektový Model
SCP.NET také poskytuje jednoduchou sadu vývojářům programovat s klíčovými objekty. Jsou **kontextu**, **úložiště stavu**, a **SCPRuntime**. V zbývající část této části jsou uvedeny.

### <a name="context"></a>Kontext
Kontext poskytuje prostředí spuštěné aplikaci. Každá instance ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) má odpovídající instance kontextu. Funkce poskytované službou kontextu je možné rozdělit do dvou částí: (1) statické části, která je k dispozici v celé C\# zpracování, (2) je dynamická část, která je dostupná jenom pro konkrétní instance kontextu.

### <a name="static-part"></a>Statické části
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` je k dispozici pro účely protokolování.

`pluginType` slouží k určení typu modulu plug-in c\# procesu. Pokud C\# je proces spuštěn v režimu místního testovacího (bez Java), typ modulu plug-in je `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` je k dispozici zobrazíte parametry konfigurace ze strany Java. Parametry jsou předány ze strany Java při C\# inicializovat modul plug-in. `Config` Parametry jsou rozděleny do dvou částí: `stormConf` a `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` je parametry definované pomocí Stormu a `pluginConf` je parametry definované podle spojovací bod služby. Příklad:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` je k dispozici získat kontext topologie, je zvláště užitečná pro komponenty s více paralelismu. Zde naleznete příklad:

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

### <a name="dynamic-part"></a>Dynamické části
Následující rozhraní se vztahují k určité instance kontextu. Instance kontextu je vytvořena platformou SCP.NET a předat kód uživatele:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Pro podporu ack netransakční spout je k dispozici následující metodu:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Pro podporu ack netransakční bolt, by měl explicitně `Ack()` nebo `Fail()` obdržel řazené kolekce členů. A když se generují nový řazené kolekce členů, kromě toho musí určovat kotvy nové řazené kolekce členů. Následující metody jsou k dispozici.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>Úložiště stavu
`StateStore` poskytuje metadatových služeb, monotónní posloupnosti generování a čekání bez koordinace. Abstrakce vyšší úrovně distribuované souběžnosti se dají `StateStore`, včetně distribuované zámky, distribuované fronty, překážek a transakce služby.

Spojovací bod služby aplikace mohou používat `State` objekt a zachová některé informace v [Apache ZooKeeper](https://zookeeper.apache.org/), zejména pro transakční topologie. To proto, pokud transakční spout dojde k chybě a restartování, můžete získat potřebné informace z ZooKeeper a restartujte kanálu.

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
    /// <returns>Uncommited States</returns>
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
    public IEnumerable<Registry> Commited();

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

Pro `Commit()` metodu, pokud simpleMode je nastavena na hodnotu true, odstraní odpovídající ZNode v ZooKeeper. V opačném případě odstraní aktuální ZNode a přidání nového uzlu v POTVRZENO\_CESTU.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime obsahuje následující dvě metody:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` slouží k inicializaci běhové prostředí spojovací bod služby. V této metodě jazyka C\# proces připojí k straně Java a získá parametry konfigurace a topologie kontext.

`LaunchPlugin()` slouží k pusťte se do smyčky zpracování zpráv. V této smyčce C\# modulu plug-in přijímá zprávy formuláře Java straně (včetně signály řazených kolekcí členů a ovládací prvek), a pak zpracování zprávy, třeba volání metody rozhraní poskytují pomocí uživatelského kódu. Vstupní parametr metody `LaunchPlugin()` je delegát, který může vrátit objekt, který ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt rozhraní implementovat.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Pro ISCPBatchBolt, abychom se mohli `StormTxAttempt` z `parms`a použít ho k posoudit, jestli je přehraná pokus. Kontrola opakování pokusu o se často provádí na bolt potvrzení a je znázorněn v `HelloWorldTx` příklad.

Obecně řečeno moduly plug-in spojovací bod služby může běžet ve dvou režimech tady:

1. Místní Test režim: V tomto režimu, moduly plug-in spojovací bod služby (C\# uživatelský kód) spustit ve fázi vývoje v sadě Visual Studio. `LocalContext` je možné v tomto režimu, který poskytuje metody k serializaci emitovaný řazené kolekce členů k místním souborům a přečtěte si je zpět do paměti.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Běžný režim: V tomto režimu je procesem java pro storm spuštěných modulů plug-in spojovací bod služby.
   
    Tady je příklad spuštění modulu plug-in spojovací bod služby:
   
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

## <a name="topology-specification-language"></a>Topologie jazyka
Určení topologie spojovací bod služby je jazyka specifického pro doménu pro popisující a konfiguraci topologie spojovací bod služby. Je založen na pro Storm Clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) a je rozšířena podle spojovací bod služby.

Specifikace topologie můžete odeslat přímo do clusteru storm pro spouštění prostřednictvím ***runspec*** příkazu.

SCP.NET přidal následující funkce pro definování transakční topologie:

| **Nové funkce** | **Parametry** | **Popis** |
| --- | --- | --- |
| **TX topolopy** |název topologie<br />spout mapy<br />bolt mapy |Definujte transakční topologii s názvem topologie &nbsp;spouts definice mapování a mapování definice funkce bolts |
| **spojovací bod služby. tx spout** |Exec – název<br />args<br />pole |Definujte transakční spout. Spuštění aplikace s ***exec-name*** pomocí ***args***.<br /><br />***Pole*** je výstupních polí pro spout |
| **spojovací bod služby tx-batch-bolt** |Exec – název<br />args<br />pole |Definujte transakční dávky Bolt. Spuštění aplikace s ***exec-name*** pomocí ***args.***<br /><br />Pole je výstupních polí pro bolt. |
| **spojovací bod služby tx potvrzení bolt** |Exec – název<br />args<br />pole |Definujte bolt transakční potvrzení. Spuštění aplikace s ***exec-name*** pomocí ***args***.<br /><br />***Pole*** je výstupních polí pro bolt |
| **nontx topolopy** |název topologie<br />spout mapy<br />bolt mapy |Definovat topologii netransakční s názvem topologie&nbsp; spouts definice mapování a mapování definice funkce bolts |
| **spout spojovací bod služby** |Exec – název<br />args<br />pole<br />parameters |Definujte netransakční spout. Spuštění aplikace s ***exec-name*** pomocí ***args***.<br /><br />***Pole*** je výstupních polí pro spout<br /><br />***Parametry*** jsou volitelné, jeho použití k zadání některých parametrů, třeba "nontransactional.ack.enabled". |
| **spojovací bod služby bolt** |Exec – název<br />args<br />pole<br />parameters |Definujte netransakční Bolt. Spuštění aplikace s ***exec-name*** pomocí ***args***.<br /><br />***Pole*** je výstupních polí pro bolt<br /><br />***Parametry*** jsou volitelné, jeho použití k zadání některých parametrů, třeba "nontransactional.ack.enabled". |

SCP.NET má definovaný následující klíčová slova:

| **klíčová slova** | **Popis** |
| --- | --- |
| **: název** |Definovat název topologie |
| **: topologie** |Definovat topologii pomocí předchozí funkce a sestavení v těch, které jsou. |
| **: p** |Definujte paralelismu nápovědu pro každý funkcích spout nebo bolt. |
| **: Konfigurace** |Definování konfigurace parametru nebo aktualizovat existující |
| **: schéma** |Definujte schéma Stream. |

A často používaných parametry:

| **Parametr** | **Popis** |
| --- | --- |
| **"plugin.name"** |Název souboru EXE modulu plug-in C# |
| **"plugin.args"** |argumenty modulu plug-in |
| **"output.schema"** |Schéma výstupu |
| **"nontransactional.ack.enabled"** |Určuje, zda je povoleno potvrzení pro topologii s netransakční |

Příkaz runspec byl nasazen spolu s bity, využívá jako:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***Prostředků dir*** parametr je nepovinný, je potřeba ho zadat, pokud chcete připojit a C\# aplikace a tento adresář obsahuje aplikace, závislostí a konfigurace.

***Cesta k třídě*** parametr je nepovinný. Používá se k určení cesta třídy Java, pokud obsahuje specifikace souboru Java Spout nebo Bolt.

## <a name="miscellaneous-features"></a>Různé funkce
### <a name="input-and-output-schema-declaration"></a>Vstup a výstup schématu deklarace
Uživatele může vysílat řazenými kolekcemi členů v C\# procesy, platformu potřebuje k serializaci řazené kolekce členů do byte [], převod na stranu Java a Storm přenášet této řazené kolekce členů k cílům. Mezitím v podřízené součásti C\# procesy přijímat řazených kolekcí členů zpět ze strany java a převést na typ původní podle platformy, všechny tyto operace jsou skryté platformou.

Pro podporu serializace a deserializace, je potřeba deklarovat schéma vstupy a výstupy uživatelského kódu.

Schéma pro vstupní a výstupní datový proud je definován jako slovník. Klíč je StreamId. Hodnota je typy sloupců. Komponenta může mít více datových proudů deklarován.

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


V kontextu objektu jsme přidali následující API:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Vývojáři musí zajistit, aby řazených kolekcí členů, protože ho dodržují schématu definované pro tento datový proud, v opačném případě systém vyvolá výjimku při běhu.

### <a name="multi-stream-support"></a>Podpora více streamů
Spojovací bod služby podporuje uživatelský kód posílat nebo přijímat z několika různých datových proudů ve stejnou dobu. Podpora odráží v objektu Context podle vygeneruje metoda přijímá parametr ID volitelné datového proudu.

Přidali jsme dvě metody v objektu SCP.NET Context. Používají se ke generování řazené kolekce členů nebo řazené kolekce členů k určení StreamId. StreamId je řetězec a musí se jednat o konzistentní vzhledem k aplikacím v obou C\# a specifikace definice topologie.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Generování do datového proudu neexistující způsobí, že výjimky modulu CLR.

### <a name="fields-grouping"></a>Seskupení polí
Předdefinované skupiny polí v Strom v SCP.NET nepracuje správně. Na straně Java Proxy všechny datové typy pole jsou ve skutečnosti byte [] a pole seskupení používá hodnota hash objektu byte [] provádět seskupení. Hodnota hash objektu byte [] je adresa tohoto objektu v paměti. Seskupení tak bude nesprávný pro dva bajty [] objektů, které sdílejí stejný obsah, ale ne stejnou adresu.

SCP.NET přidá metodu přizpůsobené seskupení a používá obsah byte [] provést seskupení. V **specifikace** soubor, je syntaxe jako:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Tady

1. "spojovací bod služby skupiny polí" znamená "Vlastní pole seskupení implementované spojovací bod služby".
2. ": tx"nebo": bez tx" znamená, že pokud je transakční topologie. Tyto informace potřebujeme, protože se liší v USA a mimo USA topologie počáteční index.
3. [0,1] znamená, že sada hash ID pole, počínaje od 0.

### <a name="hybrid-topology"></a>Hybridní topologie
Nativní Storm je napsána v jazyce Java. A SCP.Net vylepšili jej a povolte C\# vývojářům umožňuje psát C\# kód pro zpracování své obchodní logiky. Ale podporuje i hybridní topologie, která obsahuje nejen C\# spoutů a boltů, ale také Java Spout/Boltů.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Zadat Java funkcí Spout/Bolt specifikace souboru
Specifikace souboru "spojovací bod služby spout" a "spojovací bod služby bolt" lze použít také k určení Java Spouts a Bolty, tady je příklad:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Tady `microsoft.scp.example.HybridTopology.Generator` je název třídy Java Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Zadejte v příkazu runSpec cesta třídy Java
Pokud chcete odeslat topologii obsahující Java Spouts nebo Bolty, musíte nejprve zkompilujte Java Spouts nebo Bolty a získat soubory Jar. Pak je třeba zadat cesta třídy java, která obsahuje soubory Jar, při odesílání topologie. Zde naleznete příklad:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Tady **příklady\\HybridTopology\\java\\cílové\\**  je ve složce obsahující soubor Jar funkcí Spout/Bolt jazyka Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializace a deserializace mezi Java a C\#
Součást bodu připojení služby zahrnuje straně Java a C\# straně. Aby bylo možné pracovat s nativní Java Spoutů a Boltů, musí být provedena serializaci/deserializaci mezi straně Java a C\# straně, jak je znázorněno v následujícím grafu.

![Diagram komponent v jazyce java odesílání do komponenty spojovací bod služby odesílá komponent v jazyce Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serializace v Javě na straně a deserializaci v jazyce C\# na straně**
   
   Nejprve poskytujeme výchozí implementace k serializaci v Javě na straně a deserializaci v jazyce C\# straně. Metoda serializace v Javě na straně se dá nastavit v specifikace souboru:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Metoda serializace v jazyce C\# straně musí být zadán v jazyce C\# uživatelský kód:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Tato výchozí implementace pracovat většinou zadaný datový typ není příliš složitý. Pro určité případy, protože uživatelský datový typ je příliš složitý nebo protože výkonu našich výchozí implementace nesplňuje požadavek na uživatele, uživatelé můžou modulu plug-in vlastní implementaci.
   
   Serializace rozhraní java straně je definována takto:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   Deserialize rozhraní v jazyce C\# straně je definován jako:
   
   veřejné rozhraní ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serializace v jazyce C\# straně a deserializace straně Java**
   
   Metoda serializace v jazyce C\# straně musí být zadán v jazyce C\# uživatelský kód:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Metoda serializace v Javě na straně musí být zadán v specifikace souboru:
   
     (spojovací bod služby spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Následuje název deserializátor "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" a "microsoft.scp.example.HybridTopology.Person" je, že je deserializovat cílové třídy dat. do.
   
   Uživatele můžete také zařadit vlastní implementaci C\# serializátor a deserializátor Java. Tento kód je rozhraní pro jazyk C\# serializátoru:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Tento kód je rozhraní pro deserializátor Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Režim bodu připojení služby hostitele
V tomto režimu může uživatel kompilaci jejich kódy, které knihovny DLL a SCPHost.exe poskytované spojovací bod služby používat k odesílání topologie. Specifikace souboru bude vypadat přibližně takto:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Tady `plugin.name` je zadán jako `SCPHost.exe` poskytované spojovací bod služby SDK. SCPHost.exe přijímá tři parametry:

1. První z nich je název knihovny DLL, která je `"HelloWorld.dll"` v tomto příkladu.
2. Druhá je název třídy, která je `"Scp.App.HelloWorld.Generator"` v tomto příkladu.
3. Třetí příkaz je název veřejné statické metody, který lze vyvolat a získat instanci ISCPPlugin.

V režimu hostitele uživatelský kód je zkompilován jako knihovnu DLL a vyvolání platformou spojovací bod služby. Spojovací bod služby platformy tak můžete získat plnou kontrolu nad celou zpracování logiky. Proto doporučujeme, abyste našim zákazníkům odeslat topologii v režimu spojovacího bodu služby hostitele, protože můžete zjednodušit vývojové prostředí a nám novější verze také přináší větší flexibilitu a zpětnou kompatibilitu.

## <a name="scp-programming-examples"></a>Příklady programování spojovací bod služby
### <a name="helloworld"></a>Hello World
**HelloWorld** je jednoduchý příklad, který znázorňuje představu o tom SCP.Net. Používá netransakční topologie s spout volá **generátor**a dvě funkce bolts volá **rozdělovač** a **čítač**. Spout **generátor** náhodně generuje věty a posílat tyto věty a **rozdělovač**. Bolt **rozdělovač** rozdělí vět do slov a generování těchto slov chcete **čítač** bolt. Bolt "čítač" slovník používá k zaznamenání počet výskytů jednotlivých slov.

Existují dva specifikace soubory **HelloWorld.spec** a **HelloWorld\_EnableAck.spec** pro účely tohoto příkladu. V C\# kódu, ho můžete zjistit, zda je povoleno potvrzení tím, že získáme pluginConf ze strany Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Pokud je povoleno potvrzení, v spout, slouží slovník pro ukládání do mezipaměti, které ještě nebyly potvrzeno řazené kolekce členů. Pokud je volána Fail(), je znovu přehrát neúspěšné řazené kolekce členů:

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
**HelloWorldTx** příklad ukazuje, jak implementovat topologii transakční. Má jeden spout volá **generátor**, volá batch bolt **partial-count**, bolt potvrzení změn s názvem **součet počtu**. Existují tři soubory txt předem vytvořené: **DataSource0.txt**, **DataSource1.txt**, a **DataSource2.txt**.

V každé transakci spout **generátor** náhodně vybere dva soubory z předem vytvořených tři soubory a generovat názvy dvou souborů **partial-count** bolt. Bolt **partial-count** získá soubor název z přijatý řazené kolekce členů, pak otevřete soubor a počet slov v tomto souboru a nakonec generování slovo číslo, které má **součet počtu** bolt. **Součet počtu** bolt shrnuje celkový počet.

K dosažení **právě jednou** sémantiku, bolt potvrzení **součet počtu** potřeba posoudit, jestli je přehraná transakce. V tomto příkladu má proměnná statický člen:

    public static long lastCommittedTxId = -1; 

Když je vytvořena ISCPBatchBolt instance, získá `txAttempt` ze vstupních parametrů:

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

Když `FinishBatch()` je volána `lastCommittedTxId` budou aktualizováni, pokud není přehraná transakce.

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
Tato topologie obsahuje Java Spout a a C\# Bolt. Použije se výchozí serializace a deserializace implementace, která poskytuje platformu spojovací bod služby. Najdete v článku **HybridTopology.spec** v **příklady\\HybridTopology** podrobné specifikace souboru, složky a **SubmitTopology.bat** pro určení Java Cesta k třídě.

### <a name="scphostdemo"></a>SCPHostDemo
Tento příklad je stejný jako HelloWorld v podstatě. Jediným rozdílem je, že uživatelský kód je zkompilován jako knihovnu DLL a topologii se odešle pomocí SCPHost.exe. V části "Spojovací bod služby hostitele režim" podrobnější vysvětlení.

## <a name="next-steps"></a>Další kroky
Příklady topologií Apache Storm, které jsou vytvořené pomocí spojovacího bodu služby najdete v následujících dokumentech:

* [Vývoj topologií C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Zpracování událostí z Azure Event Hubs pomocí Apache Storm v HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Zpracování dat snímače vozidla ze služby Event Hubs pomocí Apache Storm v HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrakce, transformace a načítání (ETL) ze služby Azure Event Hubs pro Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
