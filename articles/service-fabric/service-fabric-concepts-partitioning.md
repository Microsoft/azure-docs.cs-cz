---
title: Vytváření oddílů služby Service Fabric | Microsoft Docs
description: Popisuje, jak při vytváření oddílů stavové služby Service Fabric. Oddíly umožňuje ukládání dat na místní počítače, data a výpočetní je možné rozšířit společně.
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
ms.openlocfilehash: bc6f25c7a8a779d949fbd09f9a9a9a37ec83f56a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="partition-service-fabric-reliable-services"></a>Spolehlivé služby oddílu Service Fabric
Tento článek obsahuje úvod do vytváření oddílů spolehlivé služby Azure Service Fabric se základními koncepty. Zdrojový kód používá v článku je také k dispozici na [Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Dělení
Dělení na oddíly není jedinečný pro Service Fabric. Ve skutečnosti je základní vzor vytváření škálovatelných služeb. V širším významu jsme vezměte v úvahu dělení jako koncept dělení stavu (data) a výpočetní do menších přístupné jednotky pro zlepšení škálovatelnosti a výkonu. Dobře známé formulář oddíly je [vytvoření oddílů dat][wikipartition], označované také jako horizontálního dělení.

### <a name="partition-service-fabric-stateless-services"></a>Bezstavové služby oddílu Service Fabric
Pro bezstavové služby si myslíte o oddílu se logická jednotka, která obsahuje jeden nebo více instancí služby. Obrázek 1 zobrazuje bezstavové služby s pět instancí, které jsou rozmístěny v clusteru pomocí jeden oddíl.

![Bezstavové služby](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Skutečně existují dva typy řešení bezstavové služby. První z nich je služba, která přetrvává stav externě, například v Azure SQL database (jako je web, který ukládá informace o relaci a data). Druhá je jen výpočetní služby (např. kalkulačky nebo bitové kopie vytváření miniatur), které nespravujete žádné trvalý stav.

Buď v případech dělení bezstavové služby je velmi výjimečných scénář--škálovatelnost a dostupnosti jsou obvykle dosáhnete přidáním více instancí. Pouze když budete chtít zvážit více oddílů pro bezstavové služby instance je, když je třeba splňovat speciální požadavky na směrování.

Jako příklad Představte si případ, kdy uživatelé s identifikátory v určitý rozsah má být zpracován pouze instance konkrétní službu. Další příklad, kdy může oddílu bezstavové služby je, pokud máte skutečně oddílů back-end (např. horizontálně dělené databáze SQL) a chcete určovat, které instanci služby musí zapsat do databáze horizontálních – nebo můžete provádět jinou práci přípravy v rámci bezstavové služby, který vyžaduje stejné rozdělení informace, jak se používá v back-end. Tyto typy scénáře lze vyřešit různými způsoby a nevyžadují nutně rozdělení do oddílů služby.

Zbývající část tohoto návodu se zaměřuje na stavové služby.

### <a name="partition-service-fabric-stateful-services"></a>Oddíl Service Fabric stavové služby
Service Fabric usnadňuje vývoj škálovatelných služeb stavová tím, že nabízí prvotřídní způsob, jak stav oddílu (data). Koncepčně, si můžete představit o oddílu stavové služby jako jednotku škálování, která je vysoce spolehlivé prostřednictvím [repliky](service-fabric-availability-services.md) které jsou distribuované a rovnoměrně rozdělen mezi uzly v clusteru.

Vytváření oddílů v kontextu Service Fabric stavové služby odkazuje na proces pro určení toho, že oddíl konkrétní službu je zodpovědná za část dokončení stav služby. (Jak je uvedeno nahoře, oddíl je sada [repliky](service-fabric-availability-services.md)). Skvělé věc o Service Fabric se umístí oddíly v různých uzlech. To umožňuje jejich dosáhnout limitu prostředků uzlu. Jako data potřebuje růst, oddíly růst a Service Fabric oddíly znovu vytvoří rovnováhu mezi uzly. Tím se zajistí nepřetržitou efektivní využití hardwarových prostředků.

Tak, abyste získali příklad, stát, že začínáte s 5 uzly clusteru a služby, který je nakonfigurovaný tak, aby měl 10 oddílů a cílem tři repliky. V takovém případě by vyvážit a distribuovat repliky do clusteru--Service Fabric a skončili byste s dva primární [repliky](service-fabric-availability-services.md) na jeden uzel.
Pokud potřebujete škálování clusteru 10 uzly, Service Fabric by znovu vyvážit primární [repliky](service-fabric-availability-services.md) pro všechny uzly 10. Podobně při změně měřítka zpět do 5 uzly Service Fabric by znovu vyvážit všechny repliky mezi 5 uzly.  

Obrázek 2 ukazuje rozdělení 10 oddílů před a po škálování clusteru.

![Stavové služby](./media/service-fabric-concepts-partitioning/partitions.png)

V důsledku toho Škálováním na více systémů se dosáhne vzhledem k tomu, že požadavky od klientů, které jsou rozmístěny v počítačích, celkový výkon aplikace je vyšší, a tím se snižuje kolize na přístup k bloky dat s.

## <a name="plan-for-partitioning"></a>Plán pro dělení
Před implementací služby, byste měli zvážit vždy rozdělení strategii, která je potřeba horizontální navýšení kapacity. Existují různé způsoby, ale všechny z nich zaměřit se na aplikace potřebuje k dosažení. V kontextu tohoto článku zvažte některé důležité aspekty.

Dobrým přístupem je myslíte o struktuře stavu, který musí být dělené jako první krok.

Podívejme jednoduchý příklad. Pokud byste chtěli vytvořit službu pro countywide dotazování, můžete vytvořit oddíl pro každé město v kraj. Potom může ukládat hlasy pro každou osobu v městě v oddílu, který odpovídá této města. Obrázek 3 znázorňuje sadu osoby a města, ve kterém se nacházejí.

![Jednoduché oddílu](./media/service-fabric-concepts-partitioning/cities.png)

Jako naplnění města se výrazně liší, můžete skončit s některé oddíly, které obsahují velké množství dat (např. Praha) a ostatní oddíly s velmi malé stavu (např. Kirkland). A co je dopad s oddíly s nerovnoměrné objemy stavu?

Pokud přemýšlíte o příklad znovu, snadno uvidíte, že oddílu, který obsahuje hlasy pro prahu získáte více požadavků než Kirkland jeden. Ve výchozím nastavení vytvoří se, že je o stejný počet primární a sekundární repliky na každém uzlu Service Fabric. Proto můžete skončit s uzly, které mají replik, které poskytovat další provoz a ostatní, které slouží menší provoz. Chcete by pokud možno se vyhnout úrovněmi horkého a studeného body takto v clusteru.

Chcete-li předejít, měli byste udělat dvě věci, z hlediska rozdělení oddílů:

* Pokuste se stav oddílu, tak, aby je rovnoměrně rozdělené mezi všechny oddíly.
* Načtení sestav z jednotlivé repliky pro službu. (Informace o tom, projděte si tento článek na [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric nabízí možnosti sestavy zatížení spotřebovávají služby, jako je množství paměti nebo počet záznamů. Na základě metrik hlášené, Service Fabric zjistí, že některé oddíly slouží větší objemy než jiné a znovu vytvoří rovnováhu clusteru přesunutím repliky pro vhodnější uzly, takže celkový je přetížena žádný uzel.

V některých případech nelze vědět, kolik dat bude v daném oddílu. Obecné doporučení je oba – nejprve přijetím strategie dělení který se šíří data rovnoměrně mezi oddílů a druhý, pomocí sestav zatížení.  První metoda zabraňuje případů popsaných v hlasování příkladu při druhý pomáhá vyhlazení dočasné rozdíly v přístupu nebo zatížení v čase.

Další aspekt plánování oddílu je vybrat správný počet oddílů na začátku.
Z hlediska Service Fabric není nic, které zabraňují začínáte s vyšší počet oddílů, než se očekává pro váš scénář.
Za předpokladu, že maximální počet oddílů ve skutečnosti je platný přístup.

Ve výjimečných případech můžete dospět nutnosti více oddílů, než jste původně vybrali. Jako počet oddílů nelze změnit po fakt, musíte použít některé pokročilé oddílu přístupy, jako je vytvoření nové instance služby stejného typu služby. Musíte také implementovat logiku některé straně klienta, která směruje požadavky v instanci správné služby založené na straně klienta znalostních bází, které musí zachovat váš klientský kód.

Je potřeba vzít v úvahu pro dělení plánování prostředky počítače k dispozici. Podle stavu, je třeba získat přístup a uložené, můžete je vázána na postupujte podle:

* Omezení šířky pásma sítě
* Omezení paměti systému
* Limity úložiště disku

Co se tak stane, pokud k omezení prostředků v clusteru s podporou spuštěné? Odpověď je, že budete jednoduše škálovat cluster tak, aby dokázala pojmout nové požadavky.

[Příručka pro plánování kapacity](service-fabric-capacity-planning.md) nabízí pokyny, jak zjistit, kolik uzly, které cluster potřebuje.

## <a name="get-started-with-partitioning"></a>Začínáme s dělení
Tato část popisuje, jak začít pracovat s vytváření oddílů služby.

Service Fabric nabízí výběr ze tří schématy oddílu:

* Pohyboval, vytváření oddílů (označováno také jako UniformInt64Partition).
* S názvem rozdělení do oddílů. Aplikace, které používají tento model obvykle mají data, která může být bucketed, v rámci ohraničené sady. Několik běžných příkladů datová pole, které jsou použity jako klíče s názvem oddílu by oblasti, PSČ, zákazníka skupiny nebo jiné obchodní hranice.
* Vytváření oddílů singleton. Singleton oddíly jsou obvykle používány, když služba nevyžaduje žádné další směrování. Například bezstavové služby použijte toto schéma rozdělení oddílů ve výchozím nastavení.

S názvem a schémata rozdělení oddílů Singleton jsou speciální formy pohyboval oddíly. Ve výchozím nastavení šablony sady Visual Studio pro použití Service Fabric pohyboval, vytváření oddílů, protože je jedním nejběžnějších a užitečné. Zbývající část tohoto článku se zaměřuje na ranged schéma rozdělení oddílů.

### <a name="ranged-partitioning-scheme"></a>Pohyboval schéma rozdělení oddílů
To slouží k určení rozsahu celé číslo (identifikovaný dolní klíč a vysoká hodnota klíče) a počet oddílů (ne). Vytvoří n oddílů, každý zodpovědná za podoblast nepřekrývají sady celkového rozsahu klíče oddílu. Například ranged schéma rozdělení oddílů s nízkou klíč 0, vysoká hodnota klíče 99 a počet 4 by vytvořit čtyři oddíly, jak je uvedeno níže.

![Vytváření oddílů v rozsahu](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Běžný postup je vytvořit hodnotu hash na základě jedinečné klíče v datové sadě. Několik běžných příkladů klíčů by vehicle identifikační číslo (VIN), ID zaměstnance nebo do jedinečného řetězce. Pomocí této jedinečný klíč by pak vygeneroval hodnotu hash, numerického zbytku klíče rozsahu, chcete použít jako klíč. Můžete zadat horní a dolní meze klíče povolený rozsah.

### <a name="select-a-hash-algorithm"></a>Vyberte algoritmus hash
Důležitou součástí generování hodnoty hash je výběr vaší algoritmus hash. Aspekt spočívá v tom, jestli je cílem chcete seskupit podobné klíče vedle sebe (polohu citlivé algoritmu hash)--nebo pokud aktivity by měl být široce distribuovány na všechny oddíly (distribuční algoritmu hash), což je dnes běžné.

Charakteristika algoritmu hash správné distribuční jsou, je snadné výpočetní, má několik kolize a rovnoměrně distribuuje klíče. Dobrým příkladem algoritmu hash efektivní je [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) algoritmus hash.

Je dobré prostředku obecné hash kód algoritmus možnosti, které [Wikipedia stránky na funkce hash](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Sestavení stavové služby s více oddílů
Umožňuje vytvořit vaše první spolehlivé stavové služby s více oddílů. V tomto příkladu vytvoříte velmi jednoduchou aplikaci, kam chcete uložit všechny poslední názvy, které začínají se stejným písmenem ve stejném oddílu.

Než napíšete kód, budete muset vezměte v úvahu oddílů a klíčů oddílů. Potřebujete 26 oddíly, (jeden pro každou písmeno abecedy), ale co o klíčích nízkou a vysokou?
Jako oznámena by měla obsahovat jeden oddíl na písmeno, můžeme použít 0 jako nízký klíč a 25 jako Vysoká hodnota klíče, jako je každý písmeno svůj vlastní klíč.

> [!NOTE]
> Toto je zjednodušený scénář, ve skutečnosti by byla nevyrovnaná distribuce. Příjmení začínající písmeny "S" nebo "M" jsou častější než ty, které jsou počínaje "X" nebo "Y".
> 
> 

1. Otevřete **sady Visual Studio** > **soubor** > **nové** > **projektu**.
2. V **nový projekt** dialogovém okně vyberte aplikace Service Fabric.
3. Volání projektu "AlphabetPartitions".
4. V **vytvoření služby** dialogovém okně vyberte **Stateful** služby a pojmenujte ji "Alphabet.Processing", jak je znázorněno na obrázku níže.
       ![Dialogové okno Nová služba v sadě Visual Studio][1]

  <!--  ![Stateful service screenshot](./media/service-fabric-concepts-partitioning/createstateful.png)-->

5. Nastavte počet oddílů. Otevřete soubor Applicationmanifest.xml umístěný ve složce ApplicationPackageRoot AlphabetPartitions projektu a aktualizovat parametr Processing_PartitionCount až 26, jak je uvedeno níže.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Také musíte aktualizovat vlastnosti LowKey a HighKey prvku StatefulService v ApplicationManifest.xml, jak je uvedeno níže.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Pro službu být přístupné otevře koncový bod na portu přidáním koncový bod elementu ServiceManifest.xml (umístěný ve složce PackageRoot) pro službu Alphabet.Processing, jak je uvedeno níže:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Služba je nyní nakonfigurován pro naslouchání na vnitřní koncový bod s 26 oddíly.
7. Dále je nutné přepsat `CreateServiceReplicaListeners()` metoda třídy zpracování.
   
   > [!NOTE]
   > Tato ukázka předpokládáme, že používáte jednoduché HttpCommunicationListener. Další informace o komunikace spolehlivé služby najdete v tématu [služba spolehlivé Service komunikační model](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Doporučené vzor pro repliku naslouchá na adresy URL je v následujícím formátu: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Proto chcete provést konfiguraci vaší komunikace naslouchací proces pro naslouchání na správné koncové body a s tohoto vzoru.
   
    Víc replik této služby může být hostovaný na stejném počítači, tak tato adresa musí být jedinečný v replice. Z tohoto důvodu ID oddílu + ID repliky jsou v adrese URL. HttpListener můžete naslouchat více adres na stejném portu, dokud předponu adresy URL je jedinečný.
   
    Nadbytečné GUID je došlo k pokročilé případu, kdy sekundární repliky také naslouchání požadavkům jen pro čtení. Pokud je to tento případ, chcete, abyste měli jistotu, že je novou jedinečnou adresu používat při přechodu z primárního na sekundární vynutit klienti pro překlad adres znovu. '+' se používá jako adresa zde tak, aby replika naslouchá na všech dostupných hostitelů (IP, FQDM localhost atd.) Následující kód ukazuje příklad.
   
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
    Naslouchání adresa URL je uvedena HttpListener. Publikovaná adresa URL je adresa URL, která je publikována ve službě Service Fabric Naming Service se používá pro zjišťování služby. Klienti požádá pro tuto adresu prostřednictvím služby zjišťování. Na adresu, kterou klienti získat musí mít skutečné IP adresu nebo plně kvalifikovaný název domény uzlu, aby bylo možné připojit. Proto je třeba nahradit '+' s uzlu IP adresu nebo plně kvalifikovaný název domény, jak je uvedeno výše.
9. Posledním krokem je přidání logika zpracování ve službě, jak je uvedeno níže.
   
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
                addResult ? "sucessfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` čte hodnoty parametru řetězce dotazu, který se používá k volání oddílu a volání `AddUserAsync` přidat lastname do slovníku spolehlivé `dictionary`.
10. Umožňuje přidat bezstavové služby na projekt a zjistěte, jak můžete volat na konkrétní oddíl.
    
    Tato služba slouží jako v jednoduchém webovém rozhraní, která přijímá lastname jako parametr řetězce dotazu, Určuje klíč oddílu a odešle ji do služby Alphabet.Processing pro zpracování.
11. V **vytvoření služby** dialogovém okně vyberte **Stateless** služby a pojmenujte ji "Alphabet.Web", jak je uvedeno níže.
    
    ![Snímek obrazovky bezstavové služby](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Aktualizujte informace o koncový bod v ServiceManifest.xml služby Alphabet.WebApi otevře port, jak je uvedeno níže.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Budete muset vracet kolekci ServiceInstanceListeners ve třídě Web. Znovu můžete implementovat jednoduché HttpCommunicationListener.
    
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
14. Nyní je třeba implementovat logiku zpracování. Volání HttpCommunicationListener `ProcessInputRequest` když přijde žádost. Proto přejdeme dopředu a přidejte následující kód.
    
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
    
    Podívejme se krok za krokem. Kód čte první písmeno parametru řetězce dotazu `lastname` do char. Potom Určuje klíč oddílu pro toto písmeno odečtením šestnáctkové hodnoty `A` z šestnáctkové hodnoty první písmeno poslední názvy.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Pamatujte si, že v tomto příkladu že používáme 26 oddíly s klíčem jeden oddíl na oddíl.
    V dalším kroku získáme oddílu služby `partition` pro tento klíč pomocí `ResolveAsync` metodu `servicePartitionResolver` objektu. `servicePartitionResolver` je definován jako
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync` Metoda trvá URI služby a klíč oddílu, zrušení token jako parametry. Služba je identifikátor URI pro službu zpracování `fabric:/AlphabetPartitions/Processing`. V dalším kroku se nám získat koncový bod oddílu.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Nakonec sestavit adresu URL koncového bodu plus řetězec dotazu a volání služby zpracování.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po dokončení zpracování výstup jsme zapsat zpět.
15. Posledním krokem je testování služby. Parametry aplikace Visual Studio používá pro místní a cloudové nasazení. Pokud chcete testovat službu s 26 oddíly místně, je potřeba aktualizovat `Local.xml` souborů ve složce ApplicationParameters AlphabetPartitions projektu, jak je uvedeno níže:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po dokončení nasazení můžete zkontrolovat služby a všechny její oddíly v Service Fabric Exploreru.
    
    ![Snímek obrazovky Service Fabric Exploreru](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. V prohlížeči, můžete otestovat Logika rozdělování zadáním `http://localhost:8081/?lastname=somename`. Zobrazí se, že každý poslední název, který začíná se stejným písmenem ukládají do stejného oddílu.
    
    ![Snímek obrazovky prohlížeče](./media/service-fabric-concepts-partitioning/samplerunning.png)

Ukázky celý zdrojový kód je k dispozici na [Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="next-steps"></a>Další postup
Informace o konceptech Service Fabric naleznete v následujících tématech:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Škálovatelnost služby Service Fabric](service-fabric-concepts-scalability.md)
* [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png