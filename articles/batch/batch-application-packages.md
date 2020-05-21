---
title: Nasazení balíčků aplikací do výpočetních uzlů
description: Pomocí funkce balíčky aplikací Azure Batch můžete snadno spravovat víc aplikací a verzí pro instalaci na výpočetních uzlech služby Batch.
ms.topic: how-to
ms.date: 04/26/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61e94ade21d8dd6fad2ba10dff87d4ba10333e3a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726872"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch

Funkce balíčků aplikací v Azure Batch poskytuje snadnou správu aplikací úkolů a jejich nasazení na výpočetní uzly ve fondu. Pomocí balíčků aplikací můžete nahrávat a spravovat více verzí aplikací, které vaše úkoly spouštějí, včetně jejich podpůrných souborů. Potom můžete automaticky nasadit jednu nebo více těchto aplikací na výpočetní uzly ve fondu.

V tomto článku se dozvíte, jak nahrávat a spravovat balíčky aplikací v Azure Portal. Pak se dozvíte, jak je nainstalovat do výpočetních uzlů fondu pomocí knihovny [Batch .NET][api_net] .

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací.
>
> Rozhraní API pro vytváření a správu balíčků aplikací jsou součástí knihovny [Batch Management .NET][api_net_mgmt] . Rozhraní API pro instalaci balíčků aplikací na výpočetním uzlu jsou součástí knihovny [Batch .NET][api_net] . Srovnatelné funkce jsou v dostupných rozhraních API služby Batch pro jiné jazyky. 
>
> Funkce balíčků aplikací, které jsou zde popsané, nahrazuje funkci Batch Apps dostupnou v předchozích verzích služby.

## <a name="application-package-requirements"></a>Požadavky na balíček aplikace
Chcete-li použít balíčky aplikací, je třeba [propojit účet Azure Storage](#link-a-storage-account) s účtem Batch.

## <a name="about-applications-and-application-packages"></a>O aplikacích a balíčcích aplikací
V rámci Azure Batch *aplikace* odkazuje na sadu binárních souborů s verzí, které se dají automaticky stáhnout do výpočetních uzlů ve fondu. *Balíček aplikace* odkazuje na *konkrétní sadu* těchto binárních souborů a představuje danou *verzi* aplikace.

![Diagram vysoké úrovně pro balíčky aplikací a aplikací][1]

### <a name="applications"></a>Aplikace
Aplikace ve službě Batch obsahuje jeden nebo více balíčků aplikace a určuje možnosti konfigurace aplikace. Například aplikace může určit výchozí verzi balíčku aplikace, která má být nainstalována na výpočetních uzlech a zda lze její balíčky aktualizovat nebo odstranit.

### <a name="application-packages"></a>Balíčky aplikací
Balíček aplikace je soubor s příponou. zip, který obsahuje binární soubory aplikace a podpůrné soubory, které se vyžadují pro vaše úlohy ke spuštění aplikace. Každý balíček aplikace představuje konkrétní verzi aplikace.

Balíčky aplikací můžete zadat na úrovni fondu a úloh. Při vytváření fondu nebo úlohy můžete určit jeden nebo více těchto balíčků a (volitelně) verzi.

* **Balíčky aplikací fondu** se nasazují do *všech* uzlů ve fondu. Aplikace se nasazují, když se uzel připojí k fondu, a když se restartuje nebo obnoví z image.
  
    Balíčky aplikací fondu jsou vhodné, pokud všechny uzly ve fondu spouštějí úkoly úlohy. Při vytváření fondu můžete určit jeden nebo více balíčků aplikace a můžete přidat nebo aktualizovat existující balíčky fondu. Pokud aktualizujete balíčky aplikací existujícího fondu, musíte restartovat své uzly a nainstalovat tak nový balíček.
* **Balíčky aplikací úloh** se nasazují jenom na výpočetní uzel naplánovaný ke spuštění úlohy těsně před spuštěním příkazového řádku úkolu. Pokud je zadaný balíček aplikace a verze již na uzlu, nedojde k opětovnému nasazení a použije se existující balíček.
  
    Balíčky aplikací úkolů jsou užitečné v prostředích se sdíleným fondem, kde se spouštějí různé úlohy v jednom fondu a fond se po dokončení úlohy neodstraní. Pokud má vaše úloha méně úkolů, než je uzlů ve fondu, balíčky aplikací úkolů můžou omezit přenosy dat, protože se aplikace může nasadit jen na uzly, které úkoly budou skutečně provádět.
  
    Další scénáře, které můžou využívat balíčky aplikací úloh, jsou úlohy, které spouštějí velkou aplikaci, ale jenom pro několik úkolů. Například fáze předběžného zpracování nebo úloha sloučení, kde je předzpracování nebo sloučení aplikace těžké, můžou využívat balíčky aplikací pro úlohy.

> [!IMPORTANT]
> Existují omezení počtu aplikací a balíčků aplikací v rámci účtu Batch a maximální velikosti balíčku aplikace. Podrobnosti o těchto omezeních najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) .
> 
> 

### <a name="benefits-of-application-packages"></a>Výhody balíčků aplikací
Balíčky aplikací můžou zjednodušit kód v řešení Batch a snížit režii potřebnou ke správě aplikací, které vaše úkoly spouštějí.

U balíčků aplikací nemusí spouštěcí úkol fondu určovat dlouhý seznam jednotlivých souborů prostředků, které se mají na uzlech nainstalovat. Nemusíte ručně spravovat více verzí souborů aplikace v Azure Storage nebo na vašich uzlech. A nemusíte se starat o generování [adres URL SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) , abyste mohli poskytovat přístup k souborům v účtu úložiště. Batch funguje na pozadí s Azure Storage k ukládání balíčků aplikací a jejich nasazování do výpočetních uzlů.

> [!NOTE] 
> Celková velikost spouštěcího úkolu nesmí přesahovat 32768 znaků, včetně souborů prostředků a proměnných prostředí. Pokud spouštěcí úkol tento limit překročí, pak je použití balíčků aplikací Další možností. Můžete také vytvořit archiv ZIP obsahující soubory prostředků, nahrát ho jako objekt BLOB a Azure Storage a pak ho rozbalit z příkazového řádku spouštěcího úkolu. 
>
>

## <a name="upload-and-manage-applications"></a>Nahrávání a Správa aplikací
Pomocí [Azure Portal][portal] nebo rozhraní API pro správu služby Batch můžete spravovat balíčky aplikací v účtu Batch. V následujících částech jsme nejdřív ukázali, jak propojit účet úložiště, a pak diskutovat o přidávání aplikací a balíčků a jejich správě s portálem.

### <a name="link-a-storage-account"></a>Propojení účtu úložiště
Chcete-li použít balíčky aplikací, musíte nejprve propojit [účet Azure Storage](batch-api-basics.md#azure-storage-account) s účtem Batch. Pokud jste ještě nenakonfigurovali účet úložiště, Azure Portal zobrazí upozornění při prvním kliknutí na **aplikace** v účtu Batch.



![Upozornění na nenakonfigurovaný účet úložiště není v Azure Portal][9]

Služba Batch používá přidružený účet úložiště k ukládání balíčků aplikací. Po propojení těchto dvou účtů může služba Batch automaticky nasadit balíčky uložené v propojeném účtu úložiště do vašich výpočetních uzlů. Pokud chcete propojit účet úložiště s účtem Batch, klikněte v okně **Upozornění** na **účet úložiště** a pak znovu klikněte na **účet úložiště** .

![V Azure Portal vyberte okno účtu úložiště.][10]

Doporučujeme vytvořit účet úložiště *konkrétně* pro použití s účtem Batch a vybrat ho tady. Po vytvoření účtu úložiště ho můžete propojit s účtem Batch pomocí okna **účtu úložiště** .

> [!IMPORTANT] 
> - V současné době nemůžete použít balíčky aplikací s účtem Azure Storage, který je nakonfigurovaný pomocí [pravidel brány firewall](../storage/common/storage-network-security.md).
> - Účet Azure Storage s **hierarchickým oborem názvů** nastaveným na **Enabled** nelze použít pro balíčky aplikací.

Služba Batch používá Azure Storage k ukládání balíčků aplikací jako objektů blob bloku. Poplatky za data objektů blob bloku se [účtují jako normální][storage_pricing] a velikost každého balíčku nemůže překročit maximální velikost objektu blob bloku. Další informace najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle pro účty úložiště](../storage/blobs/scalability-targets.md). Nezapomeňte vzít v úvahu velikost a počet balíčků aplikací a pravidelně odebírat zastaralé balíčky pro minimalizaci nákladů.

### <a name="view-current-applications"></a>Zobrazit aktuální aplikace
Pokud chcete zobrazit aplikace ve vašem účtu Batch, klikněte v levé nabídce na položku nabídky **aplikace** a zobrazte si **účet Batch**.

![Dlaždice aplikace][2]

Po výběru této možnosti nabídky se otevře okno **aplikace** :

![Výpis aplikací][3]

V tomto okně se zobrazuje ID jednotlivých aplikací ve vašem účtu a následující vlastnosti:

* **Balíčky**: počet verzí přidružených k této aplikaci.
* **Výchozí verze**: verze aplikace je nainstalovaná, pokud při určování aplikace pro fond neurčíte verzi. Toto nastavení je volitelné.
* **Povolit aktualizace**: hodnota, která určuje, zda jsou povoleny aktualizace balíčků, odstraňování a přidání. Pokud je toto nastavení nastaveno na **ne**, aktualizace balíčků a odstranění jsou pro aplikaci zakázané. Přidat lze pouze nové verze balíčku aplikace. Výchozí hodnota je **Yes** (Ano).

Pokud chcete zobrazit strukturu souborů balíčku aplikace na výpočetním uzlu, přejděte na portál na účet Batch. Z účtu Batch přejděte na **fondy**. Vyberte fond, který obsahuje výpočetní uzel (y), na které vás zajímáte.

![Uzly ve fondu][13]

Po výběru fondu přejděte do výpočetního uzlu, na kterém je balíček aplikace nainstalovaný. Odtud jsou podrobnosti balíčku aplikace umístěny ve složce **aplikace** . Další složky na výpočetním uzlu obsahují další soubory, jako jsou například úlohy spuštění, výstupní soubory, výstup chyb atd.

![Soubory na uzlu][14]

### <a name="view-application-details"></a>Zobrazit podrobnosti o aplikaci
Chcete-li zobrazit podrobnosti o aplikaci, vyberte aplikaci v okně **aplikace** .

![Podrobnosti o aplikaci][4]

V podrobnostech o aplikaci můžete pro svou aplikaci nakonfigurovat následující nastavení.

* **Povolení aktualizací**: Určete, jestli se mají jeho balíčky aplikací aktualizovat nebo odstranit. Viz část "aktualizace nebo odstranění balíčku aplikace" dále v tomto článku.
* **Výchozí verze**: zadejte výchozí balíček aplikace, který se nasadí do výpočetních uzlů.
* **Zobrazovaný název**: zadejte popisný název, který může vaše řešení Batch použít, když zobrazuje informace o aplikaci, například v uživatelském rozhraní služby, které poskytnete vašim zákazníkům prostřednictvím služby Batch.

### <a name="add-a-new-application"></a>Přidat novou aplikaci
Chcete-li vytvořit novou aplikaci, přidejte balíček aplikace a zadejte nové jedinečné ID aplikace. První balíček aplikace, který přidáte s novým ID aplikace, vytvoří také novou aplikaci.

Klikněte na **aplikace**  >  **Přidat**.

![Okno Nová aplikace v Azure Portal][5]

**Nové okno aplikace** poskytuje následující pole k určení nastavení nového aplikace a balíčku aplikace.

**ID aplikace**

Toto pole určuje ID vaší nové aplikace, které podléhá standardním ověřovacím pravidlům Azure Batch ID. Pravidla pro poskytnutí ID aplikace jsou následující:

* V uzlech systému Windows ID může obsahovat libovolnou kombinaci alfanumerických znaků, spojovníků a podtržítka. V uzlech systému Linux jsou povoleny pouze alfanumerické znaky a podtržítka.
* Nemůže obsahovat více než 64 znaků.
* Musí být v rámci účtu Batch jedinečný.
* Rozlišuje velká a malá písmena.

**Verze**

Toto pole určuje verzi balíčku aplikace, kterou nahráváte. Pro řetězce verze se vztahují následující pravidla ověřování:

* V uzlech systému Windows může řetězec verze obsahovat libovolnou kombinaci alfanumerických znaků, spojovníků, podtržítka a tečky. V uzlech systému Linux může řetězec verze obsahovat pouze alfanumerické znaky a podtržítka.
* Nemůže obsahovat více než 64 znaků.
* Musí být v rámci aplikace jedinečné.
* Zachovává velká a malá písmena.

**Balíček aplikace**

Toto pole určuje soubor. zip, který obsahuje binární soubory aplikace a podpůrné soubory, které jsou požadovány ke spuštění aplikace. Klikněte na tlačítko **Vybrat soubor** nebo na ikonu složky a vyhledejte a vyberte soubor. zip, který obsahuje soubory vaší aplikace.

Po výběru souboru kliknutím na tlačítko **OK** zahajte nahrávání na Azure Storage. Po dokončení operace nahrávání se na portálu zobrazí oznámení. V závislosti na velikosti souboru, který nahráváte, a rychlosti síťového připojení může tato operace nějakou dobu trvat.

> [!WARNING]
> Nezavírejte okno **nové aplikace** před dokončením operace nahrávání. Tím se zastaví proces nahrávání.
> 
> 

### <a name="add-a-new-application-package"></a>Přidat nový balíček aplikace
Chcete-li přidat verzi balíčku aplikace pro existující aplikaci, vyberte aplikaci v oknech **aplikace** a klikněte na **balíčky**  >  **Přidat**.

![Okno Přidat balíček aplikace v Azure Portal][8]

Jak vidíte, pole se shodují s hodnotami v okně **Nová aplikace** , ale pole **ID aplikace** je zakázané. Stejně jako u nové aplikace zadejte **verzi** nového balíčku, vyhledejte soubor **Package** . zip a kliknutím na tlačítko **OK** balíček nahrajte.

### <a name="update-or-delete-an-application-package"></a>Aktualizace nebo odstranění balíčku aplikace
Chcete-li aktualizovat nebo odstranit existující balíček aplikace, otevřete podrobnosti aplikace, klikněte na možnost **balíčky**, klikněte na **tři tečky** na řádku balíčku aplikace, který chcete upravit, a vyberte akci, kterou chcete provést.

![Aktualizace nebo odstranění balíčku v Azure Portal][7]

**Aktualizace**

Po kliknutí na tlačítko **aktualizovat**se zobrazí okna **aktualizace balíčku** . Toto okno je podobné **novému oknu balíčku aplikace** , ale je povolené jenom pole pro výběr balíčku, které umožňuje zadat nový soubor zip, který se má nahrát.

![Okno aktualizace balíčku v Azure Portal][11]

**Odstranit**

Po kliknutí na **Odstranit**se zobrazí výzva k potvrzení odstranění verze balíčku a dávka odstraní balíček z Azure Storage. Odstraníte-li výchozí verzi aplikace, bude pro aplikaci odebrána **výchozí nastavení verze** .

![Odstranit aplikaci][12]

## <a name="install-applications-on-compute-nodes"></a>Instalace aplikací na výpočetní uzly
Teď, když jste se naučili, jak spravovat balíčky aplikací pomocí Azure Portal, můžeme diskutovat o tom, jak je nasadit do výpočetních uzlů a jak je spouštět s úkoly Batch.

### <a name="install-pool-application-packages"></a>Instalovat balíčky aplikací fondu
Chcete-li nainstalovat balíček aplikace na všech výpočetních uzlech ve fondu, zadejte jeden nebo více *odkazů* na balíčky aplikací pro daný fond. Balíčky aplikací, které zadáte pro fond, se nainstalují do každého výpočetního uzlu, když se tento uzel připojí k fondu, a když se uzel restartuje nebo obnoví z image.

V rozhraní Batch .NET zadejte jednu nebo více [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] při vytváření nového fondu nebo pro stávající fond. Třída [ApplicationPackageReference][net_pkgref] Určuje ID a verzi aplikace, která se má nainstalovat na výpočetní uzly fondu.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Pokud se nasazení balíčku aplikace z nějakého důvodu nepovede, služba Batch označí uzel jako [nepoužitelný][net_nodestate]a žádné úlohy se naplánují pro provádění na tomto uzlu. V takovém případě byste měli **restartovat** uzel a znovu zahájit nasazení balíčku. Restartování uzlu také umožňuje znovu naplánovat úlohu na uzlu.
> 
> 

### <a name="install-task-application-packages"></a>Instalovat balíčky aplikací úloh
Podobně jako fond určujete *odkazy* na balíčky aplikací pro úlohu. Když je naplánováno spuštění úlohy na uzlu, balíček je stažen a extrahován těsně před provedením příkazového řádku úkolu. Pokud je v uzlu již nainstalován zadaný balíček a verze, balíček se nestáhne a použije se existující balíček.

Chcete-li nainstalovat balíček aplikace úkolu, nakonfigurujte [CloudTask][net_cloudtask]úlohy. Vlastnost [ApplicationPackageReferences][net_cloudtask_pkgref] :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Spustit nainstalované aplikace
Balíčky, které jste zadali pro fond nebo úlohu, se stáhnou a extrahují do pojmenovaného adresáře v rámci `AZ_BATCH_ROOT_DIR` uzlu. Batch také vytvoří proměnnou prostředí, která obsahuje cestu k pojmenovanému adresáři. Příkazové řádky úkolu používají tuto proměnnou prostředí při odkazování aplikace na uzlu. 

V uzlech systému Windows je proměnná v následujícím formátu:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

V uzlech se systémem Linux se formát mírně liší. Tečky (.), spojovníky (-) a znaménko čísla (#) jsou shrnuty do podtržítek v proměnné prostředí. Všimněte si také, že se zachová případ ID aplikace. Například:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`a `version` jsou hodnoty, které odpovídají verzi aplikace a balíčku, který jste zadali pro nasazení. Pokud jste například zadali, že by měla být na uzlech systému Windows nainstalována verze 2,7 nástroje *Blend* pro aplikace, budou příkazové řádky úlohy používat pro přístup ke svým souborům tuto proměnnou prostředí:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

V uzlech systému Linux zadejte proměnnou prostředí v tomto formátu. Rozveďte do podtržítek tečky (.), spojovníky (-) a znaménko (#) a zachovejte velikost ID aplikace:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Při nahrávání balíčku aplikace můžete zadat výchozí verzi, která se má nasadit do výpočetních uzlů. Pokud jste pro aplikaci zadali výchozí verzi, můžete při odkazování na aplikaci vynechat příponu verze. Výchozí verzi aplikace můžete zadat v Azure Portal v okně **aplikace** , jak je znázorněno v části [nahrání a Správa aplikací](#upload-and-manage-applications).

Například pokud nastavíte "2,7" jako výchozí verzi pro *Blend*aplikace a vaše úkoly odkazují na následující proměnnou prostředí, budou vaše uzly Windows spouštět verzi 2,7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Následující fragment kódu ukazuje příklad příkazového řádku úlohy, který spouští výchozí verzi aplikace *Blend* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Další informace o nastavení prostředí výpočetních uzlů najdete v tématu [nastavení prostředí pro úlohy](batch-api-basics.md#environment-settings-for-tasks) v [přehledu funkcí služby Batch](batch-api-basics.md) .
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu
Pokud je už existující fond nakonfigurovaný pomocí balíčku aplikace, můžete pro tento fond zadat nový balíček. Pokud zadáte nový odkaz na balíček pro fond, platí následující:

* Služba Batch nainstaluje nově zadaný balíček na všech nových uzlech, které se připojí k fondu, a na všech stávajících uzlech, které se restartují nebo obnoví z image.
* Výpočetní uzly, které už jsou ve fondu, když aktualizujete odkazy na balíček, neinstalují automaticky nový balíček aplikace. Aby bylo možné získat nový balíček, musí se tyto výpočetní uzly restartovat nebo obnovit z image.
* Při nasazení nového balíčku odrážejí vytvořené proměnné prostředí odkazy na nové balíčky aplikací.

V tomto příkladu má existující fond verzi 2,7 aplikace *Blendu* nakonfigurovanou jako jednu z jeho [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Chcete-li aktualizovat uzly fondu pomocí verze 2.76 b, zadejte nový [ApplicationPackageReference][net_pkgref] s novou verzí a potvrďte změnu.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Teď, když je nová verze nakonfigurovaná, služba Batch nainstaluje verzi 2.76 b na jakýkoliv *Nový* uzel, který se připojí k fondu. Pokud chcete nainstalovat 2.76 b na uzlech, které *už* jsou ve fondu, restartujte je nebo je znovu naimagí. Všimněte si, že restartované uzly uchovávají soubory z předchozích nasazení balíčků.

## <a name="list-the-applications-in-a-batch-account"></a>Výpis aplikací v účtu Batch
Pomocí [ApplicationOperations][net_appops]můžete v účtu Batch zobrazit seznam aplikací a jejich balíčků. Metoda [ListApplicationSummaries][net_appops_listappsummaries]

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Zabalit
Pomocí balíčků aplikací můžete svým zákazníkům pomáhat vybrat aplikace pro své úlohy a určit přesnou verzi, která se má použít při zpracování úloh pomocí služby pro dávkovou práci. Můžete také poskytnout zákazníkům možnost nahrávat a sledovat vlastní aplikace ve vaší službě.

## <a name="next-steps"></a>Další kroky
* [REST API dávky][api_rest] také poskytují podporu pro práci s balíčky aplikací. Podívejte se například na element [applicationPackageReferences][rest_add_pool_with_packages] v části [Přidání fondu k účtu][rest_add_pool] , kde najdete informace o tom, jak určit balíčky k instalaci pomocí REST API. Podrobnosti o tom, jak získat informace o aplikaci pomocí dávkové REST API, najdete v tématu [aplikace][rest_applications] .
* Naučte se programově [Spravovat účty Azure Batch a kvóty pomocí rozhraní Batch Management .NET](batch-management-dotnet.md). Knihovna [.NET Batch Management][api_net_mgmt] může povolit funkce vytváření a odstraňování účtů pro vaši aplikaci nebo službu Batch.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram vysoké úrovně balíčků aplikací"
[2]: ./media/batch-application-packages/app_pkg_02.png "Dlaždice aplikace v Azure Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Okno aplikace v Azure Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Okno podrobností aplikace v Azure Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Okno Nová aplikace v Azure Portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Rozevírací seznam aktualizovat nebo odstranit balíčky v Azure Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Okno nového balíčku aplikace v Azure Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nejedná se o výstrahu propojeného účtu úložiště."
[10]: ./media/batch-application-packages/app_pkg_10.png "V Azure Portal vyberte okno účtu úložiště."
[11]: ./media/batch-application-packages/app_pkg_11.png "Okno aktualizace balíčku v Azure Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Dialogové okno pro potvrzení odstranění balíčku v Azure Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Informace o výpočetním uzlu v Azure Portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Soubory na výpočetním uzlu zobrazeném v Azure Portal"
