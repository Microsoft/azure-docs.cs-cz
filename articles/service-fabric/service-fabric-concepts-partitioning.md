---
title: Služby partitioning Service Fabric
description: Popisuje, jak rozdělit stavové služby Service Fabric. Oddíly umožňuje ukládání dat na místních počítačích, takže data a výpočetní prostředky lze škálovat společně.
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 1f3ee2196bad8b8a0c992ed498d40b4cf5820f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258613"
---
# <a name="partition-service-fabric-reliable-services"></a>Dělení spolehlivých služeb Service Fabric
Tento článek obsahuje úvod k základním konceptům rozdělení spolehlivých služeb Azure Service Fabric. Zdrojový kód použitý v článku je také k dispozici na [GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Dělení
Dělení není jedinečné pro service fabric. Ve skutečnosti se jedná o základní vzor budování škálovatelných služeb. V širším smyslu můžeme uvažovat o dělení jako o konceptu dělení stavu (dat) a výpočetním výpočtu do menších přístupných jednotek pro zlepšení škálovatelnosti a výkonu. Dobře známá forma dělení je [dělení dat][wikipartition], označované také jako dělení.

### <a name="partition-service-fabric-stateless-services"></a>Bezstavové služby partition Service Fabric
Pro bezstavové služby můžete přemýšlet o oddíl je logická jednotka, která obsahuje jednu nebo více instancí služby. Obrázek 1 znázorňuje bezstavovou službu s pěti instancemi distribuovanými v clusteru pomocí jednoho oddílu.

![Služba bez státní příslušnosti](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Existují opravdu dva typy bezstavových řešení služeb. První z nich je služba, která přetrvává jeho stav externě, například v databázi Azure SQL (jako je web, který ukládá informace o relaci a data). Druhá je pouze výpočetní služby (jako kalkulačka nebo miniatury obrázků), které nespravují žádný trvalý stav.

V obou případech dělení bezstavové služby je velmi vzácný scénář -- škálovatelnost a dostupnost jsou obvykle dosaženo přidáním další instance. Chcete-li zvážit více oddílů pro instance bezstavové služby, je pouze v případě, že potřebujete splnit zvláštní požadavky na směrování.

Jako příklad zvažte případ, kdy uživatelé s ID v určitém rozsahu by měly být obsluhovány pouze konkrétní instance služby. Dalším příkladem, kdy byste mohli rozdělit bezstavovou službu, je, když máte skutečně rozdělený back-end (např. rozdělenou databázi SQL) a chcete řídit, která instance služby by měla zapisovat do databázového oddílu-- nebo provést další přípravné práce v rámci bezstavová služba, která vyžaduje stejné informace o dělení, které se používají v back-endu. Tyto typy scénářů lze také vyřešit různými způsoby a nemusí nutně vyžadovat dělení služby.

Zbývající část tohoto návodu se zaměřuje na stavové služby.

### <a name="partition-service-fabric-stateful-services"></a>Stavové služby partition Service Fabric
Service Fabric usnadňuje vývoj škálovatelných stavových služeb tím, že nabízí prvotřídní způsob, jak rozdělit stav (data). Koncepčně si můžete představit oddíl stavové služby jako jednotky škálování, která je vysoce spolehlivá prostřednictvím replik, které jsou distribuovány a [vyváženy](service-fabric-availability-services.md) mezi uzly v clusteru.

Dělení v kontextu service fabric stavové služby odkazuje na proces určení, že konkrétní oddíl služby je zodpovědný za část úplného stavu služby. (Jak již bylo zmíněno dříve, oddíl je sada [replik](service-fabric-availability-services.md)). Skvělá věc, o Service Fabric je, že umístí oddíly na různých uzlech. To jim umožňuje růst na limit prostředků uzlu. Jak se zvětšují potřeby dat, oddíly rostou a Service Fabric znovu vyvažuje oddíly mezi uzly. Tím je zajištěno pokračující efektivní využívání hardwarových prostředků.

Chcete-li uvést příklad, řekněme, že začnete s clusterem s 5 uzlovými daty a službou, která je nakonfigurována tak, aby měla 10 oddílů a cíl tří replik. V takovém případě service fabric by vyvážit a distribuovat repliky v rámci clusteru – a skončíte se dvěma primární [repliky](service-fabric-availability-services.md) na uzel.
Pokud nyní potřebujete horizontální navýšení kapacity clusteru na 10 uzlů, Service Fabric by znovu vyvážit primární [repliky](service-fabric-availability-services.md) ve všech 10 uzlech. Podobně pokud jste škálování zpět na 5 uzlů, Service Fabric by znovu vyvážit všechny repliky přes 5 uzlů.  

Obrázek 2 znázorňuje rozdělení 10 oddílů před a po škálování clusteru.

![Státem provozný servis](./media/service-fabric-concepts-partitioning/partitions.png)

V důsledku toho je horizontální navýšení kapacity dosaženo, protože požadavky od klientů jsou distribuovány mezi počítači, celkový výkon aplikace je lepší a tvrzení o přístupu k blokůdat je snížena.

## <a name="plan-for-partitioning"></a>Plán pro dělení
Před implementací služby byste měli vždy zvážit strategii dělení, která je nutná k horizontálnímu navýšení kapacity. Existují různé způsoby, ale všechny z nich se zaměřují na to, co aplikace potřebuje k dosažení. V kontextu tohoto článku zvažme některé důležitější aspekty.

Dobrým přístupem je přemýšlet o struktuře stavu, který je třeba rozdělit, jako první krok.

Vezměme si jednoduchý příklad. Pokud byste měli vytvořit službu pro celostátní hlasování, můžete vytvořit oddíl pro každé město v kraji. Pak můžete uložit hlasy pro každou osobu ve městě v oddílu, který odpovídá tomuto městu. Obrázek 3 znázorňuje soubor lidí a město, ve kterém se nacházejí.

![Jednoduchý oddíl](./media/service-fabric-concepts-partitioning/cities.png)

Vzhledem k tomu, že počet obyvatel měst se značně liší, můžete skončit s některými oddíly, které obsahují velké množství dat (např. Takže jaký je dopad s oddíly s nerovnoměrným množstvím stavu?

Pokud si myslíte, že o příkladu znovu, můžete snadno vidět, že oddíl, který drží hlasy pro Seattle dostane větší provoz než Kirkland jeden. Ve výchozím nastavení Service Fabric zajišťuje, že je přibližně stejný počet primárnía sekundární repliky na každém uzlu. Takže můžete skončit s uzly, které drží repliky, které slouží větší provoz a další, které slouží menší provoz. Ty by nejlépe chtěli, aby se zabránilo horké a studené skvrny, jako je tento v clusteru.

Abyste tomu zabránili, měli byste udělat dvě věci, z hlediska rozdělení:

* Pokuste se rozdělit stav tak, aby byl rovnoměrně rozložen napříč všemi oddíly.
* Ohlásit zatížení z každé repliky pro službu. (Informace o tom, jak, podívejte se na tento článek o [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric poskytuje možnost sestavy zatížení spotřebované služby, jako je například množství paměti nebo počet záznamů. Na základě hlášených metrik service fabric zjistí, že některé oddíly obsluhují vyšší zatížení než ostatní a znovu vyvažuje cluster přesunutím replik do vhodnějších uzlů, takže celkový počet nozu je přetížený.

Někdy nemůžete vědět, kolik dat bude v daném oddílu. Takže obecné doporučení je dělat obojí – za prvé přijetím strategie dělení, která rovnoměrně šíří data mezi oddíly a za druhé, a to vykazováním zatížení.  První metoda zabraňuje situacím popsaným v příkladu hlasování, zatímco druhá pomáhá vyhladit dočasné rozdíly v přístupu nebo zatížení v čase.

Dalším aspektem plánování oddílů je zvolit správný počet oddílů začít.
Z hlediska Service Fabric neexistuje nic, co vám brání začít s vyšším počtem oddílů, než se očekávalo pro váš scénář.
Ve skutečnosti za předpokladu, že maximální počet oddílů je platný přístup.

Ve výjimečných případech může nakonec potřebovat více oddílů, než jste původně zvolili. Vzhledem k tomu, že počet oddílů nelze po faktu změnit, budete muset použít některé rozšířené přístupy oddílu, například vytvoření nové instance služby stejného typu služby. Budete také muset implementovat některé logiky na straně klienta, která směruje požadavky na správnou instanci služby, na základě znalostí na straně klienta, které musí udržovat váš klientský kód.

Dalším aspektem pro plánování dělení je dostupné prostředky počítače. Vzhledem k tomu, že stát musí být přístupný a uložený, jste povinni následovat:

* Omezení šířky pásma sítě
* Limity systémové paměti
* Limity diskového úložiště

Co se tedy stane, když narazíte na omezení prostředků v běžícím clusteru? Odpověď je, že můžete jednoduše horizontální navýšení kapacity clusteru tak, aby vyhovovaly novým požadavkům.

[Průvodce plánováním kapacity](service-fabric-capacity-planning.md) nabízí pokyny, jak zjistit, kolik uzlů váš cluster potřebuje.

## <a name="get-started-with-partitioning"></a>Začínáme s dělením
Tato část popisuje, jak začít s rozdělením služby.

Service Fabric nabízí výběr ze tří schémat oddílů:

* Dělení v rozsahu (jinak známé jako UniformInt64Partition).
* Pojmenované dělení. Aplikace používající tento model mají obvykle data, která lze v rámci ohraničené sady vykonané. Některé běžné příklady datových polí používaných jako pojmenované klíče oddílů by byly oblasti, PSČ, skupiny zákazníků nebo jiné obchodní hranice.
* Singleton dělení. Singleton oddíly se obvykle používají v případě, že služba nevyžaduje žádné další směrování. Například bezstavové služby používají toto schéma dělení ve výchozím nastavení.

Pojmenované a Singleton dělení schémata jsou speciální formy oddíly s rozsahem. Ve výchozím nastavení visual studio šablony pro service fabric použití dělení v rozsahu, protože je nejběžnější a užitečné. Zbývající část tohoto článku se zaměřuje na schéma dělení v rozsahu.

### <a name="ranged-partitioning-scheme"></a>Schéma dělení v rozsahu
Používá se k určení rozsahu celé číslo (identifikované nízkým klíčem a vysokým klíčem) a počtu oddílů (n). Vytvoří n oddíly, každý zodpovědný za nepřekrývající podrozsah celkového rozsahu klíčů oddílu. Například schéma dělení v rozsahu s nízkým klíčem 0, vysokým klíčem 99 a počtem 4 by vytvořilo čtyři oddíly, jak je znázorněno níže.

![Dělení rozsahu](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Běžným přístupem je vytvoření hash založené na jedinečném klíči v rámci sady dat. Některé běžné příklady klíčů by identifikační číslo vozidla (VIN), ID zaměstnance nebo jedinečný řetězec. Pomocí tohoto jedinečného klíče byste pak vygenerovali kód hash, modul rozsahu klíčů, který chcete použít jako klíč. Můžete určit horní a dolní hranice povoleného rozsahu klíčů.

### <a name="select-a-hash-algorithm"></a>Výběr algoritmu hash
Důležitou součástí hašování je výběr algoritmu hash. Zvažte, zda je cílem seskupit podobné klíče blízko sebe (zapisování hodnot itliáše) – nebo zda by aktivita měla být distribuována široce napříč všemi oddíly (hash ování distribuce), což je běžnější.

Charakteristiky algoritmu hash dobré distribuce jsou, že je snadné vypočítat, má málo kolizí a distribuuje klíče rovnoměrně. Dobrým příkladem efektivního algoritmu hash je algoritmus hash [FNV-1.](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)

Dobrým zdrojem pro obecné volby algoritmu algoritmu hash kódu je [stránka Wikipedie o funkcích hash](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Vytvoření stavové služby s více oddíly
Pojďme vytvořit první spolehlivé stavové služby s více oddíly. V tomto příkladu vytvoříte velmi jednoduchou aplikaci, ve které chcete uložit všechna příjmení začínající stejným písmenem ve stejném oddílu.

Než začnete psát jakýkoli kód, musíte přemýšlet o oddíly a klíče oddílů. Potřebujete 26 oddílů (jeden pro každé písmeno v abecedě), ale co nízké a vysoké klíče?
Jak jsme doslova chcete mít jeden oddíl na písmeno, můžeme použít 0 jako nízký klíč a 25 jako vysoký klíč, protože každé písmeno je jeho vlastní klíč.

> [!NOTE]
> Jedná se o zjednodušený scénář, protože ve skutečnosti by distribuce byla nerovnoměrná. Příjmení začínající písmeny "S" nebo "M" jsou častější než jména začínající písmeny "X" nebo "Y".
> 
> 

1. Otevřete > **New**nový > **projekt****souboru** **sady Visual Studio** > .
2. V dialogovém okně **Nový projekt** zvolte aplikaci Service Fabric.
3. Zavolejte projekt "AlphabetPartitions".
4. V **dialogovém** okně Vytvořit službu zvolte **Stavová** služba a nazvěte ji "Alphabet.Processing".
5. Nastavte počet oddílů. Otevřete soubor Applicationmanifest.xml umístěný ve složce ApplicationPackageRoot projektu AlphabetPartitions a aktualizujte parametr Processing_PartitionCount na 26, jak je znázorněno níže.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Je také nutné aktualizovat Vlastnosti LowKey a HighKey elementu StatefulService v applicationManifest.xml, jak je znázorněno níže.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Aby byla služba přístupná, otevřete koncový bod na portu přidáním koncového prvku ServiceManifest.xml (umístěného ve složce PackageRoot) pro službu Alphabet.Processing, jak je znázorněno níže:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Nyní je služba nakonfigurována tak, aby naslouchala internímu koncovému bodu s 26 oddíly.
7. Dále je třeba přepsat `CreateServiceReplicaListeners()` metodu Processing třídy.
   
   > [!NOTE]
   > Pro tuto ukázku předpokládáme, že používáte jednoduchý HttpCommunicationListener. Další informace o spolehlivé komunikaci se službami naleznete v [tématu Model komunikace spolehlivé služby](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Doporučený vzor pro adresu URL, na které replika `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`naslouchá, je následující formát: .
    Takže chcete nakonfigurovat naslouchací proces komunikace poslouchat na správné koncové body a s tímto vzorem.
   
    Ve stejném počítači může být hostováno více replik této služby, takže tato adresa musí být pro repliku jedinečná. To je důvod, proč oddíl ID + ID repliky jsou v ADRESE URL. HttpListener může poslouchat na více adres na stejném portu, pokud je jedinečná předpona adresy URL.
   
    Další identifikátor GUID je k dispozici pro pokročilý případ, kde sekundární repliky také naslouchat jen pro čtení požadavků. V takovém případě se chcete ujistit, že se při přechodu z primární na sekundární použije klienti k opětovnému vyřešení adresy novou jedinečnou adresou. '+' se zde používá jako adresa, takže replika naslouchá všem dostupným hostitelům (IP, FQDN, localhost atd.) Níže uvedený kód ukazuje příklad.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Je také třeba poznamenat, že publikovaná adresa URL se mírně liší od předpony url poslechu.
    Adresa URL naslouchání je dána HttpListener. Publikovaná adresa URL je adresa URL publikovaná ve službě Service Fabric Naming Service Service, která se používá pro zjišťování služby. Klienti budou žádat o tuto adresu prostřednictvím této služby zjišťování. Adresa, kterou klienti získají, musí mít skutečnou IP nebo reqdn uzlu, aby se mohli připojit. Takže musíte nahradit '+' s IP uzlu nebo FQDN, jak je uvedeno výše.
9. Posledním krokem je přidání logiky zpracování do služby, jak je znázorněno níže.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest`Přečte hodnoty parametru řetězce dotazu použitého `AddUserAsync` k volání oddílu a `dictionary`zavolá přidání příjmení do spolehlivého slovníku .
10. Přidáme bezstavovou službu do projektu a zobrazíte tak, jak můžete volat konkrétní oddíl.
    
    Tato služba slouží jako jednoduché webové rozhraní, které přijímá příjmení jako parametr řetězce dotazu, určuje klíč oddílu a odešle jej službě Alphabet.Processing ke zpracování.
11. V **dialogovém** okně Vytvořit službu zvolte **bezstavová** služba a nazvěte ji "Alphabet.Web", jak je znázorněno níže.
    
    ![Snímek obrazovky služby bez státní příslušnosti](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Aktualizujte informace o koncovém bodu ve službě ServiceManifest.xml služby Alphabet.WebApi a otevřete port, jak je znázorněno níže.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Je třeba vrátit kolekci ServiceInstanceListeners ve třídě webu. Opět můžete implementovat jednoduchý HttpCommunicationListener.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Nyní je třeba implementovat logiku zpracování. HttpCommunicationListener volá `ProcessInputRequest` při přijde požadavek. Takže pojďme do toho a přidat kód níže.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Projdeme si to krok za krokem. Kód přečte první písmeno parametru `lastname` řetězce dotazu do znaku. Potom určí klíč oddílu pro toto písmeno odečtením šestnáctkové `A` hodnoty z šestnáctkové hodnoty prvního písmene příjmení.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Nezapomeňte, že v tomto příkladu používáme 26 oddílů s jedním klíčem oddílu na oddíl.
    Dále získáme oddíl `partition` služby pro tento `ResolveAsync` klíč `servicePartitionResolver` pomocí metody na objektu. `servicePartitionResolver`je definována jako
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    Metoda `ResolveAsync` přebírá identifikátor URI služby, klíč oddílu a token zrušení jako parametry. Identifikátor URI služby pro `fabric:/AlphabetPartitions/Processing`službu zpracování je . Dále získáme koncový bod oddílu.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Nakonec vytvoříme adresu URL koncového bodu plus řetězec dotazu a zavoláme službu zpracování.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po dokončení zpracování zapíšeme výstup zpět.
15. Posledním krokem je testování služby. Visual Studio používá parametry aplikace pro místní a cloudové nasazení. Chcete-li otestovat službu s 26 oddíly `Local.xml` místně, je třeba aktualizovat soubor ve složce ApplicationParameters projektu AlphabetPartitions, jak je znázorněno níže:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po dokončení nasazení můžete zkontrolovat službu a všechny její oddíly v Průzkumníku infrastruktury služby.
    
    ![Průzkumník Service Fabric – snímek obrazovky](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. V prohlížeči můžete otestovat logiku `http://localhost:8081/?lastname=somename`dělení zadáním . Uvidíte, že každé příjmení, které začíná stejným písmenem, je uloženo ve stejném oddílu.
    
    ![Snímek obrazovky prohlížeče](./media/service-fabric-concepts-partitioning/samplerunning.png)

Celý zdrojový kód ukázky je k dispozici na [GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Spolehlivé služby a objekt actor forking podprocesy
Service Fabric nepodporuje spolehlivé služby a následně spolehlivé objekty actor forking podprocesy. Příkladem toho, proč jeho není podporován je [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) nelze použít k registraci nepodporované ho dílčího procesu a tokeny zrušení jsou odesílány pouze do registrovaných procesů; Výsledkem jsou všechny druhy problémů, jako jsou chyby upgradu, když se dílčí procesy neuzavřou poté, co nadřazený proces obdrží token zrušení. 

## <a name="next-steps"></a>Další kroky
Informace o konceptech Service Fabric naleznete v následujících tématech:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Škálovatelnost služeb Service Fabric](service-fabric-concepts-scalability.md)
* [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
