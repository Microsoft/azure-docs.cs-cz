---
title: Dělení Service Fabric služeb
description: Popisuje, jak rozdělit Service Fabric stavové služby. Oddíly umožňují úložiště dat na místních počítačích, aby se data a výpočetní prostředky mohly škálovat dohromady.
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: d33e7b5ee293cf9dfb49e509bec2e1950033a956
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005424"
---
# <a name="partition-service-fabric-reliable-services"></a>Dělení spolehlivých služeb Service Fabric
Tento článek poskytuje Úvod do základních konceptů vytváření oddílů služby Azure Service Fabric Reliable Services. Zdrojový kód používaný v článku je také k dispozici na [GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Dělení
Dělení není pro Service Fabric jedinečné. Ve skutečnosti se jedná o základní vzor vytváření škálovatelných služeb. V širším smyslu můžeme uvažovat o dělení jako konceptu rozdělení stavu (dat) a výpočetních prostředků do menších dostupných jednotek za účelem zlepšení škálovatelnosti a výkonu. Dobře známá forma dělení je [dělení dat][wikipartition], označovaná také jako horizontálního dělení.

### <a name="partition-service-fabric-stateless-services"></a>Rozdělení bezstavových služeb Service Fabric
U bezstavových služeb se můžete domnívat, že oddíl je logická jednotka, která obsahuje jednu nebo víc instancí služby. Obrázek 1 zobrazuje bezstavovou službu s pěti instancemi distribuovanými napříč clusterem pomocí jednoho oddílu.

![Bezstavová služba](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Existují dva typy bezstavových řešení služby. První je služba, která uchovává svůj stav externě, například v databázi v Azure SQL Database (jako web, na kterém jsou uloženy informace o relaci a data). Druhá je jenom výpočetní služby (například Kalkulačka nebo miniatura obrázku), které nespravují žádný trvalý stav.

V obou případech je vytváření oddílů bez stavové služby velmi výjimečný scénář – škálovatelnost a dostupnost se obvykle dosahují přidáním dalších instancí. Jediný čas, kdy chcete zvážit více oddílů pro instance bezstavových služeb, je třeba v případě, že potřebujete splnit speciální požadavky směrování.

Příkladem může být případ, kdy uživatelé s ID v určitém rozsahu mají být obsluhováni pouze určitou instancí služby. Dalším příkladem, kdy můžete rozdělit bezstavovou službu, je, že máte skutečně dělenou back-end (například databázi horizontálně dělené v SQL Database) a chcete určit, která instance služby by měla zapisovat do databáze horizontálních oddílů, nebo provést jinou přípravnou práci v rámci bezstavové služby, která vyžaduje stejné informace k dělení, jako se používá v back-endu. Tyto typy scénářů je také možné vyřešit různými způsoby a nemusí nutně vyžadovat dělení služby.

Zbývající část tohoto Názorného postupu se zaměřuje na stavové služby.

### <a name="partition-service-fabric-stateful-services"></a>Rozdělit Service Fabric stavové služby
Service Fabric usnadňuje vývoj škálovatelných stavových služeb tím, že nabízí první třídu způsob rozdělení stavu (dat). V podstatě si můžete představit oddíl stavové služby jako jednotku škálování, která je vysoce spolehlivá prostřednictvím [replik](service-fabric-availability-services.md) , které jsou distribuované a vyvážené napříč uzly v clusteru.

Dělení v kontextu Service Fabric stavových služeb se týká procesu určování, zda je konkrétní oddíl služby zodpovědný za součást kompletního stavu služby. (Jak je uvedeno dříve, oddíl je sada [replik](service-fabric-availability-services.md)). Skvělé je o Service Fabric, že umístí oddíly do různých uzlů. To umožňuje rozšířit na limit prostředků uzlu. Vzhledem k nárůstu velikosti dat, zvětšování oddílů a Service Fabric přerovnává oddíly napříč uzly. Tím se zajistí nepřetržité používání hardwarových prostředků.

Řekněme například, že zahájíte cluster s pěti uzly a službu, která je nakonfigurovaná tak, aby měla 10 oddílů a cíl tří replik. V takovém případě by služba Service Fabric vyrovnala a distribuuje repliky v rámci clusteru – a měli byste mít dvě primární [repliky](service-fabric-availability-services.md) na jeden uzel.
Pokud teď potřebujete škálovat cluster na 10 uzlů, Service Fabric by znovu vyrovnal primární [repliky](service-fabric-availability-services.md) ve všech 10 uzlech. Podobně platí, že pokud se škáluje zpátky na 5 uzlů, Service Fabric by přerovnala všechny repliky v pěti uzlech.  

Obrázek 2 ukazuje distribuci 10 oddílů před a po škálování clusteru.

![Stavová služba](./media/service-fabric-concepts-partitioning/partitions.png)

V důsledku toho se dosáhne horizontálního navýšení kapacity, protože žádosti od klientů jsou distribuované napříč počítači, zlepšuje se celkový výkon aplikace a dojde ke snížení kolizí obsahu při přístupu k datovým blokům.

## <a name="plan-for-partitioning"></a>Plánování dělení
Před implementací služby byste měli vždy zvážit strategii dělení, která je nutná k horizontálnímu navýšení kapacity. Existují různé způsoby, ale všechny jsou zaměřeny na to, co aplikace potřebuje dosáhnout. V souvislosti s tímto článkem se podíváme na některé z důležitějších aspektů.

Dobrým přístupem je uvažovat o struktuře stavu, který je nutné rozdělit, jako první krok.

Pojďme se na to dát jednoduchý příklad. Pokud jste chtěli vytvořit službu pro dotazování na úrovni jednotlivých zemí, mohli byste vytvořit oddíl pro každé město v okrese. Potom můžete ukládat hlasy pro každého uživatele v městu v oddílu, který odpovídá příslušnému města. Obrázek 3 znázorňuje sadu lidí a města, ve kterých se nacházejí.

![Jednoduchý oddíl](./media/service-fabric-concepts-partitioning/cities.png)

Vzhledem k tomu, že se populace měst liší, můžete se setkat s některými oddíly, které obsahují velké množství dat (například Seattle) a dalších oddílů s velmi malým stavem (např. Kirkland). Takže to bude mít vliv na oddíly s nerovnoměrným množstvím stavu?

Pokud si o tomto příkladu myslíte znovu, můžete snadno zjistit, že oddíl, který obsahuje hlasy pro Seattle, získá více provozu, než je Kirkland 1. Ve výchozím nastavení Service Fabric zajistí, že se na každém uzlu nachází přibližně stejný počet primárních a sekundárních replik. Takže můžete končit uzly, které obsahují repliky, které obsluhují více provozu a jiné, které obsluhují méně provozu. Měli byste se vyhnout tomu, aby se v clusteru vyhnula horká a studená skvrná.

Abyste tomu předešli, měli byste provést dvě věci z oddílu s oddíly zobrazení:

* Pokuste se rozdělit stav tak, aby byl rovnoměrně rozložen mezi všechny oddíly.
* Načtení sestavy z každé repliky pro službu. (Informace o tom, jak, najdete v tomto článku v tématu [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric poskytuje možnost nahlásit zatížení spotřebované službami, jako je například množství paměti nebo počet záznamů. Na základě ohlášených metrik Service Fabric detekuje, že některé oddíly obsluhují větší objemy než jiné a znovu vyrovnávají cluster tím, že přesouvá repliky do vhodnějších uzlů, takže celkový žádný uzel není přetížený.

V některých případech nemůžete zjistit, kolik dat se v daném oddílu nachází. Obecným doporučením je tedy provést jak první, tak, že přijmete strategii vytváření oddílů, která rovnoměrně rozšíří data napříč oddíly a druhou, prostřednictvím zatížení vytváření sestav.  První metoda zabrání situacím popsaným v hlasovacím příkladu, zatímco druhá umožňuje hladké dočasné rozdíly v přístupu nebo načítání v průběhu času.

Dalším aspektem plánování oddílů je výběr správného počtu oddílů, které mají být začínat.
Z Service Fabric perspektivy není nic, co vám zabrání ve spuštění s větším počtem oddílů, než se očekává pro váš scénář.
Ve skutečnosti předpokládáme, že maximální počet oddílů je platný přístup.

Ve výjimečných případech může docházet k tomu, že budete potřebovat více oddílů, než jste původně zvolili. Nemůžete-li po faktu změnit počet oddílů, je nutné použít některé pokročilé přístupy k oddílům, například vytvoření nové instance služby se stejným typem služby. Také je nutné implementovat určitou logiku na straně klienta, která směruje požadavky na správnou instanci služby na základě znalostí na straně klienta, které musí udržovat váš kód klienta.

Dalším aspektem pro plánování dělení jsou dostupné prostředky počítače. Protože je potřeba mít k dispozici a uložený stav, budete vázáni na následující:

* Omezení šířky pásma sítě
* Omezení systémové paměti
* Omezení diskového úložiště

Co se stane, když ve spuštěném clusteru spustíte omezení prostředků? Odpověď je, že můžete jednoduše škálovat cluster tak, aby vyhovoval novým požadavkům.

[Průvodce plánováním kapacity](service-fabric-capacity-planning.md) nabízí pokyny, jak zjistit, kolik uzlů vaše cluster potřebuje.

## <a name="get-started-with-partitioning"></a>Začínáme s vytvářením oddílů
Tato část popisuje, jak začít s vytvářením oddílů služby.

Service Fabric nabízí výběr tří schémat oddílu:

* Dělení do rozsahu (jinak označované jako UniformInt64Partition).
* Pojmenovaný oddíling. Aplikace, které používají tento model, mají obvykle data, která lze v rámci ohraničené sady sestavovat. Mezi běžné příklady datových polí používaných jako klíče pojmenovaných oddílů patří oblasti, poštovní směrovací čísla, skupiny zákazníků nebo jiné obchodní hranice.
* Dělení singleton. Oddíly singleton se obvykle používají, pokud služba nevyžaduje žádné další směrování. Například bezstavové služby ve výchozím nastavení používají toto schéma dělení.

Schémata oddílů s názvem a s jedním oddílem jsou speciálními formami oddílů s rozsahem. Ve výchozím nastavení používají šablony sady Visual Studio pro Service Fabric oddíly s rozsahem, protože se jedná o nejběžnější a užitečný. Zbývající část tohoto článku se zaměřuje na škálované schéma dělení.

### <a name="ranged-partitioning-scheme"></a>Schéma dělení na rozsahy
Slouží k zadání celočíselného rozsahu (identifikovaného s nízkým klíčem a vysokým klíčem) a počtu oddílů (n). Vytvoří n oddílů, z nichž každý zodpovídá za překrývající se dílčí rozsah celkového rozsahu klíče oddílu. Například škálované schéma dělení s nízkou úrovní klíče 0, vysoký klíč 99 a počet 4 vytvoří čtyři oddíly, jak je znázorněno níže.

![Dělení rozsahu](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Běžným přístupem je vytvoření hodnoty hash na základě jedinečného klíče v sadě dat. Mezi běžné příklady klíčů patří identifikační číslo vozidla (VIN), ID zaměstnance nebo jedinečný řetězec. Pomocí tohoto jedinečného klíče byste pak vygenerovali kód hash, který vyřadí rozsah klíče, který se použije jako klíč. Můžete zadat horní a dolní meze povoleného rozsahu klíčů.

### <a name="select-a-hash-algorithm"></a>Vyberte algoritmus hash.
Důležitou součástí algoritmu hash je vybírání algoritmu hash. Je třeba zvážit, zda je cílem seskupení podobných klíčů v blízkosti každé druhé (hodnota hash citlivých na rozlišení)--nebo zda má být aktivita distribuována napříč všemi oddíly (hodnota hash distribuce), což je běžnější.

Charakteristika správného algoritmu hash distribuce je, že je snadné ho vypočítat, má několik kolizí a rovnoměrně distribuuje klíče. Dobrým příkladem efektivního algoritmu hash je algoritmus hash [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) .

Dobrým prostředkem pro obecné volby algoritmu hash je [Stránka Wikipedii na funkcích hash](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Vytvoření stavové služby s více oddíly
Vytvoříme vaši první spolehlivou stavovou službu s více oddíly. V tomto příkladu vytvoříte velmi jednoduchou aplikaci, ve které chcete uložit všechny poslední názvy, které začínají stejným písmenem ve stejném oddílu.

Než začnete psát kód, musíte se zamyslet na oddílech a klíčích oddílů. Budete potřebovat 26 oddílů (jeden pro každé písmeno v abecedním), ale co se týká nízkého a horního klíče?
Protože doslova chceme mít jeden oddíl na každé písmeno, můžeme jako nízký klíč použít 0 a 25 jako vysokého klíče, protože každé písmeno je jeho vlastní klíč.

> [!NOTE]
> Toto je zjednodušený scénář, protože v realitě je distribuce nesudá. Příjmení začínající písmeny "S" nebo "M" jsou běžnější než čísla začínající znakem "X" nebo "Y".
> 
> 

1. Otevřete **soubor aplikace Visual Studio**  >  **File**  >  **Nový**  >  **projekt**.
2. V dialogovém okně **Nový projekt** vyberte aplikaci Service Fabric.
3. Zavolejte na projekt "AlphabetPartitions".
4. V dialogovém okně **vytvořit službu** vyberte **stavová** služba a zavolejte ji "abecední. zpracování".
5. Nastavte počet oddílů. Otevřete Applicationmanifest.xml soubor umístěný ve složce ApplicationPackageRoot projektu AlphabetPartitions a aktualizujte parametr Processing_PartitionCount na 26, jak je znázorněno níže.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Také je třeba aktualizovat vlastnosti LowKey a HighKey elementu StatefulService v ApplicationManifest.xml, jak je znázorněno níže.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Aby služba byla přístupná, otevřete koncový bod na portu přidáním prvku koncového bodu ServiceManifest.xml (nachází se ve složce PackageRoot) pro službu abecedy pro zpracování abecedy, jak je znázorněno níže:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Služba je teď nakonfigurovaná tak, aby naslouchala internímu koncovému bodu s 26 oddíly.
7. Dále je nutné přepsat `CreateServiceReplicaListeners()` metodu třídy zpracování.
   
   > [!NOTE]
   > V této ukázce předpokládáme, že používáte jednoduchý HttpCommunicationListener. Další informace o komunikaci spolehlivé služby najdete v tématu [komunikační model spolehlivé služby](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Doporučený vzor pro adresu URL, na které replika naslouchá, je následující formát: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` .
    Takže chcete naslouchací proces komunikace nakonfigurovat tak, aby naslouchal na správných koncových bodech a pomocí tohoto modelu.
   
    Více replik této služby může být hostováno ve stejném počítači, takže tato adresa musí být pro repliku jedinečná. To je důvod, proč ID oddílu + ID repliky jsou v adrese URL. HttpListener může naslouchat na více adresách na stejném portu, pokud je předpona adresy URL jedinečná.
   
    Dodatečný identifikátor GUID je pro pokročilé případy, kdy sekundární repliky také naslouchají žádostem jen pro čtení. V takovém případě chcete zajistit, aby se při přechodu z primárního na sekundární používala nová jedinečná adresa, která vynutí, aby klienti přeložili adresu znovu. znak + se používá jako adresa, aby replika naslouchala na všech dostupných hostitelích (IP adresa, plně kvalifikovaný název domény, localhost atd.). Níže uvedený kód ukazuje příklad.
   
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
   
    Také je potřeba poznamenat, že publikovaná adresa URL je mírně odlišná od předpony adresy URL pro naslouchání.
    Adresa URL naslouchání je dána HttpListener. Publikovaná adresa URL je adresa URL, která je publikovaná na Service Fabric Naming Service, která se používá pro zjišťování služby. Klienti budou žádat o tuto adresu prostřednictvím této služby zjišťování. Adresa, kterou klienti získají, musí mít skutečnou IP adresu nebo plně kvalifikovaný název domény uzlu, aby se mohl připojit. Proto je třeba nahradit znak "+" adresou IP nebo plně kvalifikovaného názvu domény uzlu, jak je uvedeno výše.
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
   
    `ProcessInternalRequest` přečte hodnoty parametru řetězce dotazu, který se používá k volání oddílu a volání `AddUserAsync` k přidání položky LastName do spolehlivého slovníku `dictionary` .
10. Pojďme do projektu přidat bezstavovou službu, abyste viděli, jak můžete volat konkrétní oddíl.
    
    Tato služba slouží jako jednoduché webové rozhraní, které přijímá hodnotu LastName jako parametr řetězce dotazu, Určuje klíč oddílu a odesílá je do služby abecedy. Processing pro zpracování.
11. V dialogovém okně **vytvořit službu** vyberte **Bezstavová** služba a zavolejte na ni "abeceda. Web", jak je znázorněno níže.
    
    ![Snímek nestavové služby](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Aktualizujte informace o koncovém bodu v ServiceManifest.xml služby abecedy. WebApi, abyste otevřeli port, jak je znázorněno níže.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Je nutné vrátit kolekci ServiceInstanceListeners do webu třídy. Znovu se můžete rozhodnout pro implementaci jednoduchého HttpCommunicationListener.
    
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
14. Nyní je nutné implementovat logiku zpracování. HttpCommunicationListener volá, `ProcessInputRequest` Když přichází požadavek. Pojďme tedy pokračovat a přidat kód níže.
    
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
    
    Podívejme se na vás krok za krokem. Kód přečte první písmeno parametru řetězce dotazu `lastname` do znaku. Pak Určuje klíč oddílu pro toto písmeno odečtením hexadecimální hodnoty `A` z šestnáctkové hodnoty příjmení "první písmeno".
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Nezapomeňte, že v tomto příkladu používáme 26 oddílů s jedním klíčem oddílu na oddíl.
    Dále získáme oddíl služby `partition` pro tento klíč pomocí `ResolveAsync` metody `servicePartitionResolver` objektu. `servicePartitionResolver` je definován jako
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync`Metoda přijímá identifikátor URI služby, klíč oddílu a token zrušení jako parametry. Identifikátor URI služby pro službu zpracování je `fabric:/AlphabetPartitions/Processing` . Dále získáme koncový bod oddílu.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Nakonec sestavíme adresu URL koncového bodu a dotaz QueryString a budeme volat službu zpracování.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po dokončení zpracování napíšeme výstup zpátky.
15. Posledním krokem je testování služby. Visual Studio používá parametry aplikace pro místní a cloudové nasazení. Chcete-li otestovat službu s 26 oddíly místně, je nutné aktualizovat `Local.xml` soubor ve složce ApplicationParameters projektu AlphabetPartitions, jak je znázorněno níže:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po dokončení nasazení můžete službu a všechny její oddíly na Service Fabric Explorer ověřit.
    
    ![Snímek obrazovky Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. V prohlížeči můžete otestovat logiku dělení zadáním `http://localhost:8081/?lastname=somename` . Uvidíte, že každý název, který začíná se stejným písmenem, je uložený ve stejném oddílu.
    
    ![Snímek obrazovky prohlížeče](./media/service-fabric-concepts-partitioning/samplerunning.png)

Celý zdrojový kód ukázky je k dispozici na [GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="next-steps"></a>Další kroky
Informace o Service Fabric konceptech najdete v následujících tématech:

* [Dostupnost služeb Service Fabric Services](service-fabric-availability-services.md)
* [Škálovatelnost služeb Service Fabric Services](service-fabric-concepts-scalability.md)
* [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
