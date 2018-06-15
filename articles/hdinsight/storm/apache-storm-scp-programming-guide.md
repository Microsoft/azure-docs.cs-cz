---
title: Průvodce programováním SCP.NET | Microsoft Docs
description: Další informace o použití SCP.NET k vytvoření. Na základě NET topologie Storm pro použití se Storm v HDInsight.
services: hdinsight
documentationcenter: ''
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: 0f4c021bc209c99e1b3f34b34bf5ba0549eb48f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421551"
---
# <a name="scp-programming-guide"></a>Průvodce programováním spojovací bod služby
Spojovací bod služby je platforma pro sestavení v reálném čase, spolehlivou a konzistentní a zpracování dat vysoce výkonné aplikace. Je založen na [Apache Storm](http://storm.incubator.apache.org/) – systém navrhovány komunit OSS zpracování datového proudu. Storm je určen podle Nathan Marz a byla open source pomocí služby Twitter. Využívá [Apache ZooKeeper](http://zookeeper.apache.org/), jiného projektu Apache umožňující vysoce spolehlivé distribuované správy koordinace a stavu. 

Pouze spojovací bod služby projektu Storm v systému Windows, která je součástí, ale také projektu přidat rozšíření a přizpůsobení pro prostředí Windows. Rozšíření jsou vývojáře prostředí .NET a knihovny, přizpůsobení zahrnuje nasazení systému Windows. 

Rozšíření a přizpůsobení se provádí tak, že je není potřeba rozvětvit projekty operačních systémů a jsme může využít odvozené ekosystémů nástavbou Storm.

## <a name="processing-model"></a>Zpracování modelu
Data v spojovací bod služby je modelovaná jako nepřetržité proudy řazené kolekce členů. Obvykle řazené kolekce členů toku do některé fronty nejprve pak zachyceny a transformovat obchodní logiky hostovanou v rámci topologie Storm, nakonec výstup může předat jako řazených kolekcí členů do jiného systému spojovací bod služby, nebo být zapsána do úložiště jako systém souborů DFS nebo databází jako SQL Server.

![Diagram fronty napájení data pro zpracování, které informační kanály úložiště dat](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Topologie aplikací v Storm, definuje výpočetní graf. Každý uzel v topologii obsahuje logiku zpracování a odkazů mezi uzly indikují datový tok. Uzly vložení vstupní data do topologie se nazývají _spouts_, který slouží k pořadí data. Vstupních dat může být umístěn v protokoly transakcí databáze, čítače výkonu systému atd. Uzly s obou toky vstupní a výstupní data, se nazývají _bolts_, který provést filtrování skutečná data a výběry a agregaci.

Spojovací bod služby podporuje snažit v aspoň jednou a přesně-po zpracování dat. V distribuované aplikaci streamování zpracování různých chybám může dojít během zpracování dat, jako je výpadek sítě, selhání počítače nebo Chyba uživatelského kódu atd. Zpracování v aspoň jednou zajistí, že všechna data zpracuje alespoň jednou stejná data přehrání automaticky, když se stane chyba. Na alespoň jedno zpracování je jednoduché a spolehlivé a vyhovuje dobře mnoho aplikací. Pokud aplikace vyžaduje přesné počítání, na alespoň jedno zpracování je ale dostatek vzhledem k tomu, že stejná data může být přehráván potenciálně v topologii aplikace. V takovém případě, přesně-po zpracování je navržená tak, abyste měli jistotu, výsledek je správný, i když data mohou přehrány a zpracovávat vícekrát.

Spojovací bod služby umožňuje vývojářům rozhraní .NET k vývoji aplikací proces reálném čase dat současně využívat v jazyce Java Virtual Machine (JVM) se Storm v pozadí. Rozhraní .NET a prostředí Java Virtual Machine komunikovat prostřednictvím místní sockets TCP. V podstatě každou funkcí Spout/Bolt je proces pár .net nebo Java, kde běží logiku uživatelského v rozhraní .net procesu jako o modul plug-in.

Sestavit aplikaci pro zpracování dat nad spojovací bod služby, jsou potřeba několik kroků:

* Návrh a implementaci funkcích Spouts stáhnout data z fronty.
* Návrh a implementaci funkce Bolts ke zpracování vstupních dat a uložit data do externího úložiště, například do databáze.
* Návrh topologie, odeslání a spusťte topologii. Topologie definuje bodů uchycení a data toků mezi vrcholů. Spojovací bod služby bude trvat specifikace topologie a nasaďte ji na cluster Storm, kde každý vrchol běží na jednom uzlu logické. Převzetí služeb při selhání a škálování se postarat Storm Plánovač úloh.

Tento dokument pomocí jednoduché příklady ukážeme, jak sestavit aplikaci pro zpracování dat se spojovací bod služby.

## <a name="scp-plugin-interface"></a>Modul plug-in rozhraní spojovací bod služby
Moduly plug-in spojovací bod služby (nebo aplikace) je samostatná souborů EXE, které můžete i spustit v prostředí Visual Studio v průběhu fáze vývoje a zapojené do kanálu Storm po nasazení v produkčním prostředí. Psaní modulu plug-in spojovací bod služby je stejně jako zápis jakékoli jiné standardní konzolové aplikace systému Windows. Platforma SCP.NET deklaruje některé rozhraní pro funkcí spout/bolt a kód modulu plug-in uživatele by měla implementovat tato rozhraní. Hlavním účelem tohoto návrhu je, že se uživatel zaměřit na své vlastní obchodní logics a nechá jiných věcí zpracovávat SCP.NET platformy.

Modul plug-in kód uživatele by měla implementovat jednu z těchto hodnot rozhraní, závisí na tom, jestli je topologie transakční nebo netransakční, a jestli je součást funkcích spout nebo bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin je společné rozhraní pro všechny typy modulů plug-in. V současné době je fiktivní rozhraní.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout je rozhraní pro netransakční funkcí spout.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Když `NextTuple()` nazývá C\# uživatelského kódu můžete emitování jeden nebo více řazené kolekce členů. Pokud není nic pro vydávání, tato metoda by měla vrátit bez generování nic. Je potřeba poznamenat, `NextTuple()`, `Ack()`, a `Fail()` se nazývají ve smyčce úzkou v jedno vlákno v jazyce C\# procesu. Pokud nejsou žádné řazených kolekcí členů pro vydávání, je zdvořilý mít NextTuple spánku krátkou dobu (například 10 ms) tak, aby odpady příliš mnoho procesoru.

`Ack()` a `Fail()` se nazývají pouze v případě potvrzení mechanismus je povolena v specifikace souboru. `seqId` Slouží k identifikaci řazené kolekce členů, které jsou acked nebo se nezdařilo. Takže pokud ack je povoleno v netransakční topologie, následující funkce vysílat by měl používat ve funkcích Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Pokud ack není podporován v netransakční topologii `Ack()` a `Fail()` může být ponecháno prázdné funkce.

`parms` Vstupní parametr těchto funkcí je prázdný slovník, je vyhrazena pro budoucí použití.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt je rozhraní pro netransakční funkcí bolt.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Pokud je k dispozici, nové řazené kolekce členů `Execute()` funkce je volána zpracovat.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout je rozhraní pro transakční funkcí spout.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Stejně jako netransakční kontrolní část `NextTx()`, `Ack()`, a `Fail()` se nazývají ve smyčce úzkou v jedno vlákno v jazyce C\# procesu. Pokud nejsou žádná data pro vydávání, je zdvořilý tak, aby měl `NextTx` režimu spánku pro krátké množství času (10 ms) tak, aby odpady příliš mnoho procesoru.

`NextTx()` je volána spustit novou transakci, výstupní parametr `seqId` slouží k identifikaci transakce, která je použita také v `Ack()` a `Fail()`. V `NextTx()`, uživatel může posílat data na straně Java. Jsou data uložena v ZooKeeper pro podporu opětovného přehrání. Kapacitu ZooKeeper je omezená, a proto by měl uživatel pouze emitování metadata, není hromadné dat v transakční funkcí spout.

Bude Storm přehráním transakce automaticky, pokud se nezdaří, tak `Fail()` by neměl být volán v případě normální. Ale pokud spojovací bod služby můžete zkontrolovat metadata vysílaných transakční spout, můžete volat `Fail()` po neplatná metadata.

`parms` Vstupní parametr těchto funkcí je prázdný slovník, je vyhrazena pro budoucí použití.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt je rozhraní pro transakční funkcí bolt.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` je volána, když je nové řazené kolekce členů přicházejících u bolt. `FinishBatch()` je volána, když je tato transakce skončila. `parms` Vstupní parametr je vyhrazena pro budoucí použití.

Pro transakční topologie, je důležité koncept – `StormTxAttempt`. Má dvě pole `TxId` a `AttemptId`. `TxId` slouží k identifikaci konkrétní transakce, a pro dané transakci, může mít více pokusů transakce selže a je přehrány. SCP.NET vytvoří nový objekt ISCPBatchBolt ke zpracování jednotlivých `StormTxAttempt`, jenom jako Storm nemá v jazyce Java. Účelem tohoto návrhu je podpora zpracování paralelní transakce. Uživatel ho měli mít na paměti, že pokud transakce pokus po dokončení, odpovídající objekt ISCPBatchBolt zničena a uvolnění z paměti.

## <a name="object-model"></a>Objektový Model
SCP.NET také poskytuje jednoduchou sadou objekty klíče pro vývojáře k programu s. Jsou **kontextu**, **úložiště stavu**, a **SCPRuntime**. Jejich jsou popsané v části rest v této části.

### <a name="context"></a>Kontext
Kontext poskytuje prostředí běžící aplikaci. Každá instance ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) má odpovídající instance kontextu. Funkce poskytované službou kontextu je možné rozdělit do dvou částí: (1) statickou část, která je k dispozici v celé C\# zpracovat, (2) na dynamické druhé, která je dostupná jenom pro konkrétní instance kontextu.

### <a name="static-part"></a>Statické části
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` pro účely protokolu poskytována.

`pluginType` slouží k označení typ modulu plug-in C\# procesu. Pokud C\# proces běží v režimu místního testovacího (bez Java), typ modulu plug-in je `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` je k dispozici získat parametry konfigurace ze strany Java. Parametry jsou předány ze strany Java při C\# modul plug-in je inicializován. `Config` Parametry jsou rozdělené do dvou částí: `stormConf` a `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` je parametry definované Storm a `pluginConf` je parametry definované spojovací bod služby. Příklad:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` je k dispozici získat kontext topologie, je nejvhodnější pro komponenty s více stupně paralelního zpracování. Zde naleznete příklad:

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
Následující rozhraní jsou relevantní pro určité instance kontextu. Instance kontextu je vytvořený SCP.NET platformy a předaný uživatelského kódu:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Pro podporu ack netransakční funkcí spout je k dispozici následující metodu:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Pro podporu ack netransakční bolt, by měl explicitně `Ack()` nebo `Fail()` řazené kolekce členů přijala. A při vytváření nové řazené kolekce členů, musíte také zadat kotvy nové řazené kolekce členů. Tyto metody jsou k dispozici.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>Úložiště stavu
`StateStore` poskytuje metadata služby, generování monotónní pořadí a bez čekání spolupráce. Abstrakce vyšší úrovně distribuované souběžnosti se dají vytvářet `StateStore`, včetně distribuované zámky, distribuované fronty, překážek a transakce služby.

Spojovací bod služby aplikace mohou používat `State` objekt, který chcete zachovat některé informace v ZooKeeper, zejména pro transakční topologii. To tím, že pokud transakční spout zhroucení a restartování, může načíst informace potřebné z ZooKeeper a restartujte kanálu.

`StateStore` Objekt především má tyto metody:

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

`State` Objekt především má tyto metody:

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

Pro `Commit()` metoda simpleMode nastavena na hodnotu true, odstraní odpovídající ZNode v ZooKeeper. Jinak, odstraní aktuální ZNode a přidání nového uzlu v potvrzení\_cesta.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime poskytuje následujících dvou metod:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` slouží k inicializaci běhové prostředí spojovací bod služby. Tato metoda C\# proces připojí k straně Java a získá parametry konfigurace a topologie kontextu.

`LaunchPlugin()` slouží k ji smyčka zpracování zpráv. V této smyčky C\# modulu plug-in přijímá zprávy formuláře Java straně (včetně řazených kolekcí členů a řízení signály) a pak zpracování zprávy, například voláním metody rozhraní poskytují pomocí uživatelského kódu. Vstupní parametr metody `LaunchPlugin()` je delegáta, který může vrátit objekt, který ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt rozhraní implementovat.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Pro ISCPBatchBolt, nám získat `StormTxAttempt` z `parms`a použít ho k posoudit, zda se jedná o přehraná pokus. Kontrola opakování pokusu o se často provádí na bolt potvrzení a je znázorněn v `HelloWorldTx` příklad.

Obecně řečeno moduly plug-in spojovací bod služby může spustit ve dvou režimech tady:

1. Místní testovací režim: V tomto režimu, moduly plug-in spojovací bod služby (C\# uživatelského kódu) spustit v průběhu fáze vývoje v sadě Visual Studio. `LocalContext` můžete použít v tomto režimu, který poskytuje metodu, jak serializovat emitovaného řazené kolekce členů do místní soubory a číst je zpět do paměti.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Regulární režim: V tomto režimu, moduly plug-in spojovací bod služby spustily procesem storm java.
   
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

## <a name="topology-specification-language"></a>Topologie specifikace jazyka
Specifikace topologie spojovací bod služby je specifické pro doménu jazyk pro popis a konfiguraci topologie spojovací bod služby. Je založena na Storm na Clojure DSL (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) a je prodloužena spojovací bod služby.

Specifikace topologie jde odeslat přímo na clusteru storm pro provedení prostřednictvím ***runspec*** příkaz.

SCP.NET přidala k definování transakční topologie následující funkce:

| **Nové funkce** | **Parametry** | **Popis** |
| --- | --- | --- |
| **TX topolopy** |název topologie<br />spout mapy<br />bolt mapy |Definovat topologii transakcí s názvem topologie &nbsp;spouts definice mapy a funkce bolts definice mapy |
| **spojovací bod služby. tx spout** |Exec – název<br />argumentů<br />pole |Definujte transakční funkcí spout. Spuštění aplikace s ***exec název*** pomocí ***argumentů***.<br /><br />***Pole*** je výstup pole pro spout |
| **spojovací bod služby tx-batch-bolt** |Exec – název<br />argumentů<br />pole |Definujte transakční Bolt dávky. Spuštění aplikace s ***exec název*** pomocí ***argumentů.***<br /><br />Pole je pole výstup pro funkcí bolt. |
| **spojovací bod služby tx potvrzení bolt** |Exec – název<br />argumentů<br />pole |Definujte bolt transakční potvrzení. Spuštění aplikace s ***exec název*** pomocí ***argumentů***.<br /><br />***Pole*** je výstup pole pro bolt |
| **nontx topolopy** |název topologie<br />spout mapy<br />bolt mapy |Definovat topologii netransakční s názvem topologie&nbsp; spouts definice mapy a funkce bolts definice mapy |
| **spout spojovací bod služby** |Exec – název<br />argumentů<br />pole<br />parameters |Definujte netransakční spout. Spuštění aplikace s ***exec název*** pomocí ***argumentů***.<br /><br />***Pole*** je výstup pole pro spout<br /><br />***Parametry*** jsou volitelné, jej použijete k zadání některých parametrů, třeba "nontransactional.ack.enabled". |
| **bolt spojovací bod služby** |Exec – název<br />argumentů<br />pole<br />parameters |Definujte netransakční funkcí Bolt. Spuštění aplikace s ***exec název*** pomocí ***argumentů***.<br /><br />***Pole*** je výstup pole pro bolt<br /><br />***Parametry*** jsou volitelné, jej použijete k zadání některých parametrů, třeba "nontransactional.ack.enabled". |

SCP.NET má následující klíčová slova definované:

| **Klíčová slova** | **Popis** |
| --- | --- |
| **: název** |Definovat název topologie |
| **: topologie** |Definovat topologii pomocí předchozí funkcí a sestavení v těch, které jsou. |
| **: p** |Definujte paralelismus nápovědu pro každou funkcí spout nebo bolt. |
| **: Konfigurace** |Definování konfigurace parametru nebo aktualizovat existující |
| **: schéma** |Definování schématu datového proudu. |

A často používané parametry:

| **Parametr** | **Popis** |
| --- | --- |
| **"plugin.name"** |Název souboru EXE modulu plug-in C# |
| **"plugin.args"** |argumentů modulu plug-in |
| **"output.schema"** |Schéma výstupu |
| **"nontransactional.ack.enabled"** |Určuje, jestli je povolená ack pro netransakční topologie |

Příkaz runspec nasazuje společně s službu bits, je použití jako:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***Prostředků dir*** parametr je nepovinný, budete muset zadat ho, když chcete zařadit a C\# aplikace a tento adresář obsahuje aplikace, závislosti a konfigurace.

***Cesty pro třídy*** parametr je nepovinný. Slouží k určení cesty pro třídy Java, pokud specifikace soubor obsahuje Java Spout nebo Bolt.

## <a name="miscellaneous-features"></a>Různé funkce
### <a name="input-and-output-schema-declaration"></a>Vstup a výstup schématu deklarace
Uživatelé mohou emitování řazených kolekcí členů v jazyce C\# procesy, platformu potřebuje serializuje řazenou kolekci členů do byte [], přenést na straně Java a Storm přenese tento řazené kolekce členů k cílům. Mezitím v podřízené součásti, C\# procesy přijímat řazených kolekcí členů zpět z straně java a ho převést na původní typy podle platformy, všechny tyto operace jsou skryté platformou.

Pro podporu serializace a deserializace, musí deklarovat schéma vstupy a výstupy uživatelského kódu.

Schéma vstupu a výstupu datového proudu je definován jako slovník. Klíč je StreamId. Hodnota je typy sloupců. Součást může mít víc datových proudů deklarován.

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


V kontextu objektu máme následující rozhraní API, které jsou přidány:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Vývojáři musí zajistit, aby řazené kolekce členů vygenerované orientují schéma definované pro tento datový proud, jinak vyvolá výjimku modulu runtime v systému.

### <a name="multi-stream-support"></a>Podpora více datového proudu
Spojovací bod služby podporuje uživatelského kódu emitování nebo přijmout z několika různých datových proudů ve stejnou dobu. Podpora odráží v kontextu objektu jako vysílat metoda přebírá parametr ID typu volitelné datového proudu.

Byly přidány dvě metody v SCP.NET kontextu objektu. Používají se pro vydávání řazené kolekce členů nebo řazené kolekce členů, které slouží k zadání StreamId. StreamId je řetězec a musí se jednat o konzistentní v obou C\# a specifikace definice topologie.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Emitování do datového proudu neexistující způsobí, že výjimky za běhu.

### <a name="fields-grouping"></a>Pole seskupení
Předdefinované seskupení pole v Strom v SCP.NET nepracuje správně. Na straně Java Proxy se všechny datové typy polí ve skutečnosti byte [] a pole seskupování používá hash byte [] objektu k seskupení. Hodnota hash objektu byte [] je adresa tohoto objektu v paměti. Seskupení tak bude nesprávný pro dva bajty [] objekty, které sdílejí stejný obsah, ale není stejnou adresu.

SCP.NET přidá metoda přizpůsobené seskupení a používá obsah byte [] udělat seskupení. V **specifikace** soubor, syntaxe je jako:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Tady

1. "spojovací bod služby skupiny polí" znamená "Vlastní pole seskupení implementované spojovací bod služby".
2. ": tx"nebo": bez tx" znamená, pokud je transakční topologie. Tyto informace potřebujeme od počáteční index se liší v tx oproti bez tx topologie.
3. [0,1] znamená hash sadu ID pole, počínaje od 0.

### <a name="hybrid-topology"></a>Hybridní topologie
Nativní Storm je napsán v jazyce Java. A SCP.Net má rozšířené jej a povolte C\# vývojářům psát C\# kód pro zpracování jejich obchodní logiku. Ale podporuje i hybridní topologie, která obsahuje nejen C\# funkcích spouts nebo funkce bolts, ale také Java funkcí Spout/funkce Bolts.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Zadat Java funkcí Spout/Bolt specifikace souboru
V souboru specifikace "spojovací bod služby funkcí spout" a "spojovací bod služby funkcí bolt" lze použít také k určení Java Spouts a funkce Bolts, tady je příklad:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Zde `microsoft.scp.example.HybridTopology.Generator` je název třídy Java Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Zadejte cestu pro třídy Java v runSpec příkaz
Pokud chcete odeslat topologie obsahující Java Spouts nebo funkce Bolts, musíte napřed zkompilovat Java Spouts nebo funkce Bolts a získání souborů Jar. Pak musíte zadat cestu pro třídy java, která obsahuje soubory Jar při odesílání topologie. Zde naleznete příklad:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Zde **příklady\\HybridTopology\\java\\cíl\\**  je ve složce obsahující soubor Jar funkcí Spout/Bolt Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializace a deserializace mezi Java a C\#
Spojovací bod služby součást zahrnuje straně Java a C\# straně. Chcete-li pracovat s nativní Java funkcích Spouts nebo funkce Bolts, musí být provedena serializaci nebo deserializaci mezi straně Java a C\# straně, jak je znázorněno v následujícím grafu.

![Diagram součásti java odesílání do komponenty spojovací bod služby odesílání do komponent v jazyce Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serializace v jazyce Java straně a deserializace v jazyce C\# straně**
   
   Nejprve poskytujeme výchozí implementace v jazyce Java straně serializace a deserializace v jazyce C\# straně. Metoda serializace v jazyce Java straně lze zadat v souboru specifikace:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Metoda deserializace v jazyce C\# straně musí být zadán v jazyce C\# uživatelského kódu:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Tato výchozí implementace by měla řídit většinou zadaný datový typ není příliš složitý. U některých případů, protože je příliš složitý uživatelský datový typ nebo protože výkon naše výchozí implementace nesplňuje požadavek na uživatele, uživatelé mohou modulu plug-in vlastní implementaci.
   
   Serializace rozhraní na straně java je definován jako:
   
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
2. **Serializace v jazyce C\# straně a deserializace na straně Java**
   
   Metoda serializace v jazyce C\# straně musí být zadán v jazyce C\# uživatelského kódu:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Metoda deserializace na straně Java musí být zadán v souboru specifikace:
   
     (spojovací bod služby spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Následuje název deserializátor "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" a "microsoft.scp.example.HybridTopology.Person" je, že cílová třída dat se deserializovat k.
   
   Uživatele můžete také zařadit vlastní implementace C\# serializátor a deserializátor Java. Tento kód je rozhraní pro C\# serializátor:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Tento kód je rozhraní pro deserializátor Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Spojovací bod služby hostitele režimu
V tomto režimu můžete uživatele zkompilovat jejich kódů DLL a používat k odesílání topologie SCPHost.exe poskytované spojovací bod služby. Specifikace soubor vypadá tento kód:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Zde `plugin.name` je zadán jako `SCPHost.exe` poskytované spojovací bod služby SDK. SCPHost.exe přijímá tři parametry:

1. První z nich je název knihovny DLL, která je `"HelloWorld.dll"` v tomto příkladu.
2. Druhá je název třídy, což je `"Scp.App.HelloWorld.Generator"` v tomto příkladu.
3. Třetí ten je název veřejné statické metody, který může vyvolat získat instanci ISCPPlugin.

V režimu hostitele uživatelský kód kompiluje jako knihovny DLL a je vyvolána platformou spojovací bod služby. Spojovací bod služby platformy, můžete získat úplné řízení pro logiku celou zpracování. Proto doporučujeme, abyste naše zákazníky, odeslání topologie v režimu hostitele spojovací bod služby, protože můžete zjednodušit vývojového prostředí a přineste nám větší flexibilitu a zpětnou kompatibilitu pro také novější verze.

## <a name="scp-programming-examples"></a>Příklady programování spojovací bod služby
### <a name="helloworld"></a>Hello World
**Hello World** je jednoduchý příklad zobrazíte chuť SCP.Net. Používá topologii netransakční s spout, nazývá **generátor**a dvě funkce bolts názvem **rozdělovače** a **čítač**. Spout **generátor** náhodně generuje věty a emitování tyto věty k **rozdělovače**. Bolt **rozdělovače** rozdělí věty na slova a emitování tyto slova **čítač** funkcí bolt. Bolt "čítač" používá slovník pro záznam výskyt počet jednotlivých slov.

Existují dva soubory specifikace, **HelloWorld.spec** a **HelloWorld\_EnableAck.spec** v tomto příkladu. V C\# kódu, ho můžete zjistit, zda je povoleno potvrzení získáním pluginConf ze strany Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Ve funkcích spout Pokud je povoleno potvrzení, slouží slovník pro ukládání do mezipaměti řazené kolekce členů, které nebyly acked. Pokud je volána Fail(), je přehrány selhání řazené kolekce členů:

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
**HelloWorldTx** příklad ukazuje, jak implementovat transakční topologie. Má jeden spout názvem **generátor**, názvem batch bolt **partial počet**, potvrzení bolt s názvem **počet součet**. Existují také tři soubory txt předem vytvořené: **DataSource0.txt**, **DataSource1.txt**, a **DataSource2.txt**.

V každou transakci, spout **generátor** náhodně vybere dva soubory z předem vytvořené tři soubory a emitování názvy dvou souborů k **partial počet** funkcí bolt. Bolt **partial počet** získá soubor název z přijaté řazené kolekce členů, pak otevřete soubor a počet slov v tomto souboru a nakonec emitování word číslo, které má **počet součet** funkcí bolt. **Počet součet** bolt shrnuje celkový počet.

K dosažení **právě jednou** sémantiku, potvrzení bolt **počet součet** potřeba posoudit, zda se jedná o přehraná transakce. V tomto příkladu má proměnná statický člen:

    public static long lastCommittedTxId = -1; 

Když je vytvořena ISCPBatchBolt instance, získá `txAttempt` ze vstupní parametry:

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

Když `FinishBatch()` je volána, `lastCommittedTxId` budou aktualizováni, pokud se nejedná o přehraná transakce.

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
Tato topologie obsahuje Java Spout a a C\# funkcí Bolt. Používá výchozí serializace a deserializace implementace poskytované spojovací bod služby platformy. Najdete v článku **HybridTopology.spec** v **příklady\\HybridTopology** složku podrobnosti specifikace souboru, a **SubmitTopology.bat** pro určení Java Cesta pro třídy.

### <a name="scphostdemo"></a>SCPHostDemo
Tento příklad je stejný jako HelloWorld v zásadě. Jediným rozdílem je, že kompilace kódu uživatele jako knihovny DLL a topologii je odeslána pomocí SCPHost.exe. Najdete v části "Spojovací bod služby hostitele režim" podrobnější vysvětlení.

## <a name="next-steps"></a>Další kroky
Příklady topologií Storm vytvořený spojovací bod služby najdete v následujících dokumentech:

* [Vývoj topologie C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Zpracování událostí z Azure Event Hubs se Storm v HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Zpracování dat snímačů vehicle ze služby Event Hubs pomocí Storm v HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrakce, transformace a načítání (ETL) ze služby Azure Event Hubs k HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
