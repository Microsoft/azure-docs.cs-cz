---
title: Dělení služeb Service Fabric | Dokumentace Microsoftu
description: Popisuje, jak dělit stavové služby Service Fabric. Oddíly umožňují ukládání dat v místních počítačích tak data a výpočetní výkon je možné škálovat společně.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: msfussell
ms.openlocfilehash: 0012304412b343918ab69abf6eababc033cddc6f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198210"
---
# <a name="partition-service-fabric-reliable-services"></a>Dělení Service Fabric reliable services
Tento článek obsahuje úvod do základních konceptech služby dělení reliable services v Azure Service Fabric. Je také k dispozici na zdrojového kódu v článku [Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Dělení
Vytváření oddílů není jedinečný pro Service Fabric. Ve skutečnosti je základní vzor vytváření škálovatelných služeb. V širší představu budeme přemýšlet o dělení jako koncept dělení stavu (data) a výpočetní do menších přístupné jednotek zlepšit škálovatelnost a výkon. Je dobře známé formě a dělení [dělení dat][wikipartition], označovaný také jako horizontální dělení.

### <a name="partition-service-fabric-stateless-services"></a>Bezstavové služby Service Fabric oddílu
U bezstavových služeb si myslíte o oddílu se logická jednotka, která obsahuje jednu nebo víc instancí služby. Obrázek 1 ukazuje bezstavovou službu s pěti instancemi distribuovat napříč clusterem pomocí jednoho oddílu.

![Bezstavové služby](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Ve skutečnosti existují dva typy bezstavové služby v řešení. První z nich je služba, která udržuje svůj stav externě, například ve službě Azure SQL database (jako je web, který ukládá informace o relaci a data). Druhá je čistě výpočetní služby (např. Kalkulačka nebo image vytváření miniatur), které nedokáže spravovat žádný trvalý stav.

V buď v případech dělení bezstavovou službu je velmi vzácný scénář – škálovatelnosti a dostupnosti jsou obvykle dosahuje tak, že přidáte další instance. Chcete vezměte v úvahu několik oddílů pro bezstavové služby instance se pouze pokud je potřeba splnit zvláštní směrování požadavků.

Jako příklad Představte si případ, kdy uživatele s ID v určitý rozsah by měl být obsluhovaný pouze instance konkrétní služby. Další příklad, kdy může rozdělit bezstavovou službu je, když máte skutečně dělené back-endu (např. horizontálně dělené databáze SQL) a vy chcete ovládací prvek by měl zapisovat do horizontálních oddílů databáze--nebo provést další přípravu práci v rámci které instanci služby Bezstavová služba, která vyžaduje dělení stejné informace jako se používá v back-endu. Tyto druhy scénářů lze vyřešit různými způsoby a nevyžadují dělení.

Zbývající část tohoto návodu se zaměřuje na stavové služby.

### <a name="partition-service-fabric-stateful-services"></a>Stavové služby Service Fabric oddílu
Service Fabric zjednodušuje vývoj škálovatelných stavové služby tím, že nabízí prvotřídní způsob, jak stav oddílu (data). Entity se dá chápat rozdělení stavové služby jako jednotka škálování, který je prostřednictvím vysoce spolehlivých [repliky](service-fabric-availability-services.md) , které jsou distribuovány a vyvažují mezi uzly v clusteru.

Vytváření oddílů v kontextu stavové služby Service Fabric odkazuje na procesu, který určuje, že oddíl konkrétní službu zodpovídá za část úplný stav služby. (Jak je uvedeno nahoře, oddíl je sada [repliky](service-fabric-availability-services.md)). Nejlepší informace o Service Fabric je umístí oddíly na různých uzlech. To umožňuje jim možnost posílit limit prostředek uzlu. Jak rostou potřeby data, oddíly růst a Service Fabric znovu vytvoří rovnováhu oddílů mezi uzly. To zajistí pokračující efektivní využívání hardwarové prostředky.

Abych vám příklad, Řekněme, že začnete s 5 uzly clusteru a služba, která má nakonfigurovanou 10 oddíly a cíl tří replik. V takovém případě by vyvážit a repliky distribuovat napříč clusterem--Service Fabric a skončili byste s dvou primárních [repliky](service-fabric-availability-services.md) podle počtu uzlů.
Pokud potřebujete horizontální navýšení kapacity clusteru na 10 uzlů, Service Fabric bude znovu vyvážit primární [repliky](service-fabric-availability-services.md) napříč všemi uzly 10. Podobně pokud můžete škálovat zpět do 5 uzlů, Service Fabric by obnovit rovnováhu všechny repliky mezi 5 uzlů.  

Obrázek 2 ukazuje rozdělení 10 oddíly před a po škálování clusteru.

![Stavové služby](./media/service-fabric-concepts-partitioning/partitions.png)

V důsledku toho horizontální navýšení kapacity je dosaženo vzhledem k tomu, že se distribuují požadavky od klientů na počítačích, zlepší se výkon aplikace a snižuje kolize přístupu k bloky dat.

## <a name="plan-for-partitioning"></a>Plán pro dělení
Před implementací služby, měli byste zvážit vždy strategie dělení, který je potřeba škálovat na více systémů. Existují různé způsoby, ale všechny z nich zaměřit na aplikace potřebuje k dosažení. Pro daný kontext tohoto článku Pojďme se podívat některé další důležité aspekty.

Přemýšlet o struktuře stavu, který potřebuje k rozdělení na oddíly, prvním krokem je dobrý nápad.

Pojďme se na jednoduchý příklad. Pokud byste chtěli vytvořit službu pro countywide dotazování, můžete vytvořit oddíl pro každé město v kraj. Hlasy pro každou osobu, která pak může uchovávat ve městě v oddílu, který odpovídá této město. Obrázek 3 ukazuje sadu uživatelů a města, ve kterém se nacházejí.

![Jednoduché oddílu](./media/service-fabric-concepts-partitioning/cities.png)

Jako počet obyvatel měst, ve kterých může být velmi různá, může skončit s některé oddíly, které obsahují velké množství dat (například v Praze) a ostatní oddíly stavem velmi malé (třeba Kirkland). Co tedy je dopad existence oddíly s nerovnoměrné množství stavu?

Pokud přemýšlíte o příklad znovu, můžete snadno zobrazit, že oddíl, který obsahuje hlasy pro Seattle získá další provoz než Kirkland jeden. Ve výchozím nastavení, Service Fabric zajišťuje, že je o stejný počet primárních a sekundárních replik na každém uzlu. Proto můžete skončit s uzly, které obsahují repliky, které další provoz a ostatní mohli poskytovat služby, které slouží menší provoz. Ideálně byste to vyhnout horké a studené body takto v clusteru.

Pokud chcete předejít, měli udělat dvě věci, z hlediska vytváření oddílů:

* Došlo k pokusu o rozdělení stavu tak, aby je rovnoměrně rozdělené mezi všechny oddíly.
* Načtení sestav z každou repliku služby. (Informace o tom, prohlédněte si tento článek na [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric poskytuje schopnost sestavy zatížení používané služby, jako je množství paměti nebo počet záznamů. Na základě metrik hlásí, Service Fabric zjistí, několik oddílů má větší zatížení než jiné a znovu vytvoří rovnováhu clusteru přesunutím replik pro vhodnější uzly, takže celkově je přetížena žádný uzel.

V některých případech nemůže vědět, kolik dat bude v daném oddílu. Obecné doporučení je oba – nejprve díky využití strategie dělení, který šíří data rovnoměrně napříč oddíly a druhé, tak, že generování sestav zatížení.  První metoda zabraňuje případů popsaných v tomto příkladu hlasování při druhém pomáhá vyhlazení dočasné rozdíly v přístupu nebo zatížení v čase.

Další aspekty plánování oddílu je začneme zvolit správný počet oddílů.
Z hlediska Service Fabric není nic, který vám brání začínáte s vyšší počet oddílů, než se očekává pro váš scénář.
Ve skutečnosti za předpokladu, že maximální počet oddílů je platný přístup.

Ve výjimečných případech může skončit nutnosti více oddílů, než jste zpočátku vybrali. Jako počet oddílů nemůžete změnit po jejich výskytu, je třeba použít některé přístupy, pokročilé oddílu, jako je vytvoření nové instance služby stejného typu služby. Musíte také implementovat některé logiku na straně klienta, která směruje požadavky na správné služby instanci, na základě znalostí na straně klienta, který musí udržovat klientský kód.

Je potřeba vzít v úvahu vytváření oddílů plánování prostředky počítače k dispozici. Jak stav musí být často a ukládají, určitě postupujte podle:

* Omezení šířky pásma sítě
* Omezení paměti systému
* Omezení úložiště disku

Co se tak stane, pokud narazíte na omezení prostředků v spuštěný cluster? Odpověď je, že můžete jednoduše škálovat cluster tak, aby vyhovovaly novým požadavkům.

[Příručka pro plánování kapacity](service-fabric-capacity-planning.md) nabízí pokyny, jak určit, kolik uzlů váš cluster potřebuje.

## <a name="get-started-with-partitioning"></a>Začínáme s dělení
Tato část popisuje, jak začít pracovat s dělením vaší služby.

Service Fabric nabízí výběr ze tří schématy oddílu:

* Rozsahové dělení (jinak známé jako UniformInt64Partition).
* S názvem rozdělení do oddílů. Aplikace pomocí tohoto modelu obvykle obsahují data, která může být kterých rozdělit, v rámci sady omezená. Mezi běžné příklady datová pole, které slouží jako pojmenovaný oddíl klíče by oblastí, PSČ, zákazníka skupiny nebo jiné obchodní hranice.
* Dělení typu singleton. Jednotlivý prvek oddíly se obvykle používají, když služba nevyžaduje žádné další směrování. Například bezstavové služby použijte toto schéma oddílů ve výchozím nastavení.

S názvem a schémata dělení typu Singleton jsou speciální formy rozsahové oddíly. Ve výchozím nastavení šablony sady Visual Studio pro Service Fabric, využijte rozsahové, dělení, protože je jedním nejběžnější a užitečné. Zbývající část tohoto článku se zaměřuje na ranged schéma rozdělení oddílů.

### <a name="ranged-partitioning-scheme"></a>Rozsah schéma vytváření oddílů
To slouží k určení oblasti celé číslo (identifikovaných podle nízká hodnota klíče a vysoká hodnota klíče) a počet oddílů (n). Vytvoří n oddíly, každý za překrývat podrozsahu celkový rozsah klíče oddílu. Například ranged schématu oddílů vzali s nízkou klíčem 0, vysoká hodnota klíče 99 a počet 4 by vytvořit čtyři oddíly, jak je znázorněno níže.

![Vytváření oddílů v rozsahu](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Běžným přístupem je vytvoří hodnotu hash na základě v rámci sady dat jedinečné klíče. Mezi běžné příklady klíče by vozidla identifikační číslo (VIN), ID zaměstnance nebo jedinečný řetězec. Pomocí tohoto klíče jedinečné, pak vygeneruje hodnotu hash, modulus rozsah klíče, který se použije jako klíč. Můžete určit horní a dolní meze povolený rozsah klíčů.

### <a name="select-a-hash-algorithm"></a>Vyberte hashovací algoritmus.
Důležitou součástí algoritmu hash je výběr hashovací algoritmus. Je potřeba, jestli je chcete seskupit podobné klíče vedle sebe (umístění citlivých algoritmu hash)--nebo pokud aktivita měla široce distribuovat na všechny oddíly (distribuční algoritmus hash), což je běžné.

Vlastnosti algoritmu hash správné distribuční jsou snadno vypočítat, má několik kolize a rovnoměrně distribuuje klíče. Dobrým příkladem efektivní hashovací algoritmus je [FNV 1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) hashovací algoritmus.

Je vhodným místem k zadání obecné hash kód algoritmu volby [stránky Wikipedia na funkce hash](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Vytvoření stavové služby s více oddílů
Pojďme vytvořit první spolehlivé stavové služby s více oddílů. V tomto příkladu vytvoříte velmi jednoduchá aplikace, kam chcete uložit všechny poslední s názvy začínajícími stejné písmeno do stejného oddílu.

Než začnete psát kód, je potřeba zamyslet, oddíly a klíče oddílů. Je třeba 26 oddíly (jeden pro každé písmeno abecedy), ale co o klíčích nízký klíč a vysoký?
Jelikož chceme doslova mít jeden oddíl na písmeno, můžeme použít 0 jako nízký klíč a 25 jako Vysoká hodnota klíče, protože každé písmeno svůj vlastní klíč.

> [!NOTE]
> Toto je zjednodušený scénář, ve skutečnosti by byla nerovnoměrné distribuce. Poslední s názvem začínajícím písmeny "S" nebo "M", jsou mnohem běžnější než ty, počínaje "X" nebo "Y".
> 
> 

1. Open **Visual Studio** > **File** > **New** > **Project**.
2. V **nový projekt** dialogového okna zvolte aplikace Service Fabric.
3. Volání projektu "AlphabetPartitions".
4. V **vytvořit službu** dialogového okna zvolte **stavová** služby a nazvat ho "Alphabet.Processing".
5. Nastavte počet oddílů. Otevřete soubor Applicationmanifest.xml ve složce ApplicationPackageRoot AlphabetPartitions projektu a aktualizovat parametr Processing_PartitionCount až 26, jak je znázorněno níže.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Také musíte aktualizovat vlastnosti LowKey a HighKey StatefulService prvku v souboru ApplicationManifest.xml, jak je znázorněno níže.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Služba přístupný otevřete koncový bod na portu tak, že přidáte element koncového bodu ServiceManifest.xml (umístěný ve složce PackageRoot) pro službu Alphabet.Processing, jak je znázorněno níže:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Služba je teď nakonfigurovaná k naslouchání na interní koncový bod s 26 oddíly.
7. Dále je třeba přepsat `CreateServiceReplicaListeners()` metoda třídy zpracování.
   
   > [!NOTE]
   > V tomto příkladu předpokládáme, že používáte jednoduché HttpCommunicationListener. Další informace o komunikaci spolehlivé služby najdete v tématu [spolehlivá služba komunikační model](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Doporučený model pro adresu URL, která naslouchá replika je v následujícím formátu: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    To chcete provést konfiguraci vašeho naslouchací proces komunikace tak, aby naslouchala na správném koncové body a v tomto modelu.
   
    Víc replik této služby mohly být hostovány na stejném počítači, tak tato adresa musí být jedinečný v replice. To je důvod, proč ID oddílu + ID repliky jsou v adrese URL. HttpListener může naslouchat na více adres na stejný port jako předponu adresy URL je jedinečný.
   
    Nadbytečné GUID je pro s pokročilé případem, kde sekundární repliky také naslouchání požadavkům na jen pro čtení. Pokud je to tento případ, budete chtít Ujistěte se, že novou jedinečnou adresu se používá při přesunu z primárního do sekundárního vynuťte u klientů pro překlad adres znovu. '+' se používá jako adresa zde tak, aby replika naslouchá na všech dostupných hostitelů (IP, plně kvalifikovaný název domény, localhost, atd.) Následující kód ukazuje příklad.
   
    ```CSharp
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
   
    Je také vhodné poznamenat, že se mírně liší od naslouchání předponu adresy URL publikovanou adresu URL.
    Naslouchání adresy URL je uvedena HttpListener. Publikované adresy URL je adresa URL, která je publikovaná služba pojmenování Service Fabric, který se používá pro zjišťování služby. Klienti se zeptá, pro tuto adresu prostřednictvím této služby zjišťování. Na adresu, kterou klienti získat musí mít skutečné IP adresu nebo plně kvalifikovaný název domény uzlu, abyste se mohli připojit. Proto je třeba nahradit '+' s uzlu IP adresu nebo plně kvalifikovaný název domény, jak je znázorněno výše.
9. Posledním krokem je přidání logika zpracování ve službě, jak je znázorněno níže.
   
    ```CSharp
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
   
    `ProcessInternalRequest` čte hodnoty parametru řetězce dotazu, který se používá k volání oddílu a volání `AddUserAsync` pro přidání do spolehlivého slovníku příjmení `dictionary`.
10. Přidejme bezstavovou službu do projektu, jak může volat konkrétní oddíl.
    
    Tato služba slouží jako jednoduché webové rozhraní, která přijímá příjmení jako parametru řetězce dotazu, určí klíč oddílu a posílá ji do služby Alphabet.Processing ke zpracování.
11. V **vytvořit službu** dialogového okna zvolte **Stateless** služby a nazvat ho "Alphabet.Web", jak je znázorněno níže.
    
    ![Snímek obrazovky s bezstavovou službu](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Aktualizujte informace o koncovém bodu v ServiceManifest.xml Alphabet.WebApi služby otevřete port, jak je znázorněno níže.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Je potřeba vrátit sadu ServiceInstanceListeners ve třídě Web. Znovu můžete implementovat jednoduché HttpCommunicationListener.
    
    ```CSharp
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
14. Teď budete muset implementovat logiku zpracování. Volání HttpCommunicationListener `ProcessInputRequest` když přijde žádost. Takže můžeme pokračovat a přidejte následující kód.
    
    ```CSharp
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
    
    Projděme si to krok za krokem. Kód načte první písmeno parametru řetězce dotazu `lastname` do typu char. Poté, určí klíč oddílu pro toto písmeno tak, že se na šestnáctkovou hodnotu `A` z šestnáctkovou hodnotu poslední názvy první písmeno.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Mějte na paměti, v tomto příkladu že používáme 26 oddíly s klíčem jeden oddíl na oddíl.
    Potom získáme oddílu služby `partition` pro tento klíč pomocí `ResolveAsync` metodu `servicePartitionResolver` objektu. `servicePartitionResolver` je definován jako
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync` Identifikátor URI služby, klíč oddílu a zrušením token jako parametry přijímá metodu. Služba je identifikátor URI služby zpracování `fabric:/AlphabetPartitions/Processing`. V dalším kroku jsme koncový bod získat v oddílu.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Nakonec jsme sestavit adresu URL koncového bodu a řetězec dotazu a volání služby zpracování.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po dokončení zpracování se výstup jsme zapsat zpět.
15. Posledním krokem je k otestování služby. Parametry aplikace Visual Studio používá pro místní a cloudové nasazení. K otestování této služby s využitím 26 oddíly místně, je potřeba aktualizovat `Local.xml` souborů ve složce ApplicationParameters AlphabetPartitions projektu, jak je znázorněno níže:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po dokončení nasazení můžete zkontrolovat služby a všechny její oddíly v Service Fabric Exploreru.
    
    ![Service Fabric Explorer – snímek obrazovky](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. V prohlížeči, můžete otestovat dělení logiky tak, že zadáte `http://localhost:8081/?lastname=somename`. Uvidíte, že každý poslední název, který se spustí se stejným písmenem ukládají do stejného oddílu.
    
    ![Snímek obrazovky prohlížeče](./media/service-fabric-concepts-partitioning/samplerunning.png)

Celý zdrojový kód ukázku je k dispozici na [Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Spolehlivé služby a větvení podprocesů, které se objekt Actor
Service Fabric se modelu reliable services a následně reliable actors větvení podprocesů, které se nepodporuje. Je například proč není podporována [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) nelze použít k registraci nepodporované podproces a zpracovává zrušení tokenů se odešlou, jenom na registrovaná; výsledkem jsou nejrůznější problémy, jako například selhání upgradu, při podprocesů, které nezavírejte, jakmile obdrží token zrušení nadřazeného procesu. 

## <a name="next-steps"></a>Další postup
Informace o konceptech Service Fabric naleznete v následujících tématech:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Škálovatelnost služeb Service Fabric](service-fabric-concepts-scalability.md)
* [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
