---
title: Nasazení balíčků aplikací do výpočetních uzlů
description: Pomocí funkce balíčky aplikací Azure Batch můžete snadno spravovat víc aplikací a verzí pro instalaci na výpočetních uzlech služby Batch.
ms.topic: how-to
ms.date: 09/16/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 0d705ca731c40563deaeb02c29da120211db7ff4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985042"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch

Funkce balíčků aplikací nástroje Azure Batch pomáhá spravovat aplikace úloh a jejich nasazení na výpočetní uzly ve fondech. Balíčky aplikací můžou zjednodušit kód v řešení Batch a snížit režii potřebnou ke správě aplikací, které vaše úkoly spouštějí. Pomocí balíčků aplikací můžete nahrávat a spravovat více verzí aplikací, které vaše úkoly spouštějí, včetně jejich podpůrných souborů. Potom můžete automaticky nasadit jednu nebo více těchto aplikací na výpočetní uzly ve fondu.

Balíčky aplikací můžou zákazníkům pomáhat při výběru aplikací pro své úlohy a určení přesné verze, která se má použít při zpracování úloh ve službě s povolenými dávkami. Zákazníkům můžete také poskytnout možnost nahrávat a sledovat vlastní aplikace ve vaší službě.

Rozhraní API pro vytváření a správu balíčků aplikací jsou součástí knihovny [Batch Management .NET](/dotnet/api/overview/azure/batch/management) . Rozhraní API pro instalaci balíčků aplikací na výpočetním uzlu jsou součástí knihovny [Batch .NET](/dotnet/api/overview/azure/batch/client) . Srovnatelné funkce jsou v dostupných rozhraních API služby Batch pro jiné jazyky.

Tento článek vysvětluje, jak nahrát a spravovat balíčky aplikací v Azure Portal a jak je nainstalovat do výpočetních uzlů fondu pomocí knihovny [Batch .NET](/dotnet/api/overview/azure/batch/client) .

## <a name="application-package-requirements"></a>Požadavky na balíček aplikace

Chcete-li použít balíčky aplikací, je třeba [propojit účet Azure Storage](#link-a-storage-account) s účtem Batch.

Existují omezení počtu aplikací a balíčků aplikací v rámci účtu Batch a maximální velikosti balíčku aplikace. Podrobnosti o těchto omezeních najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) .

> [!NOTE]
> Fondy dávek vytvořené před 5. července 2017 nepodporují balíčky aplikací (Pokud se nevytvořily po 10. březnu 2016 pomocí Cloud Services konfigurace).
>
> Funkce balíčků aplikací, které jsou zde popsané, nahrazuje funkci Batch Apps dostupnou v předchozích verzích služby.

## <a name="about-applications-and-application-packages"></a>O aplikacích a balíčcích aplikací

V rámci Azure Batch *aplikace* odkazuje na sadu binárních souborů s verzí, které se dají automaticky stáhnout do výpočetních uzlů ve fondu. *Balíček aplikace* odkazuje na konkrétní sadu těchto binárních souborů, které představují danou verzi aplikace.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagram znázorňující zobrazení aplikací a balíčků aplikací na vysoké úrovni.":::

*Aplikace* ve službě Batch obsahuje jeden nebo více balíčků aplikace a určuje možnosti konfigurace aplikace. Například aplikace může určit výchozí verzi balíčku aplikace, která má být nainstalována na výpočetních uzlech a zda lze její balíčky aktualizovat nebo odstranit.

*Balíček aplikace* je soubor s příponou. zip, který obsahuje binární soubory aplikace a podpůrné soubory, které se vyžadují pro vaše úlohy ke spuštění aplikace. Každý balíček aplikace představuje konkrétní verzi aplikace. Podporován je pouze formát. zip.

Balíčky aplikací můžete zadat na úrovni fondu nebo úkolů. Při vytváření fondu nebo úlohy můžete určit jeden nebo více těchto balíčků a (volitelně) verzi.

- **Balíčky aplikací fondu** se nasazují do všech uzlů ve fondu. Aplikace se nasazují, když se uzel připojí k fondu, a když se restartuje nebo obnoví z image.
  
    Balíčky aplikací fondu jsou vhodné, pokud všechny uzly ve fondu spouštějí úkoly úlohy. Při vytváření fondu můžete určit jeden nebo více balíčků aplikace a můžete přidat nebo aktualizovat existující balíčky fondu. Pokud aktualizujete balíčky aplikací existujícího fondu, musíte restartovat své uzly a nainstalovat tak nový balíček.

- **Balíčky aplikací úloh** se nasazují jenom na výpočetní uzel naplánovaný ke spuštění úlohy těsně před spuštěním příkazového řádku úkolu. Pokud je zadaný balíček aplikace a verze již na uzlu, nedojde k opětovnému nasazení a použije se existující balíček.
  
    Balíčky aplikací úkolů jsou užitečné v prostředích se sdíleným fondem, kde se spouštějí různé úlohy v jednom fondu a fond se po dokončení úlohy neodstraní. Pokud má vaše úloha méně úkolů, než je uzlů ve fondu, balíčky aplikací úkolů můžou omezit přenosy dat, protože se aplikace může nasadit jen na uzly, které úkoly budou skutečně provádět.
  
    Další scénáře, které můžou využívat balíčky aplikací úloh, jsou úlohy, které spouštějí velkou aplikaci, ale jenom pro několik úkolů. Například fáze předběžného zpracování nebo úloha sloučení, kde je předzpracování nebo sloučení aplikace těžké, můžou využívat balíčky aplikací pro úlohy.

U balíčků aplikací nemusí spouštěcí úkol fondu určovat dlouhý seznam jednotlivých souborů prostředků, které se mají na uzlech nainstalovat. Nemusíte ručně spravovat více verzí souborů aplikace v Azure Storage nebo na vašich uzlech. A nemusíte se starat o generování [adres URL SAS](../storage/common/storage-sas-overview.md) , abyste mohli poskytovat přístup k souborům v účtu úložiště. Batch funguje na pozadí s Azure Storage k ukládání balíčků aplikací a jejich nasazování do výpočetních uzlů.

> [!NOTE]
> Celková velikost spouštěcího úkolu nesmí přesahovat 32768 znaků, včetně souborů prostředků a proměnných prostředí. Pokud spouštěcí úkol tento limit překročí, pak je použití balíčků aplikací Další možností. Můžete také vytvořit soubor. zip, který obsahuje soubory prostředků, nahrát ho jako objekt BLOB a Azure Storage a pak ho rozbalit z příkazového řádku spouštěcího úkolu.

## <a name="upload-and-manage-applications"></a>Nahrávání a Správa aplikací

Pomocí [Azure Portal](https://portal.azure.com) nebo rozhraní API pro správu služby Batch můžete spravovat balíčky aplikací v účtu Batch. V následujících částech jsme nejdřív ukázali, jak propojit účet úložiště, a pak diskutovat o přidávání aplikací a balíčků a jejich správě s portálem.

### <a name="link-a-storage-account"></a>Propojení účtu úložiště

Chcete-li použít balíčky aplikací, je třeba propojit [účet Azure Storage](accounts.md#azure-storage-accounts) s účtem Batch. Služba Batch použije přidružený účet úložiště k ukládání balíčků aplikací. Doporučujeme vytvořit účet úložiště konkrétně pro použití s účtem Batch.

Pokud jste ještě nenakonfigurovali účet úložiště, Azure Portal zobrazí upozornění při prvním výběru **aplikací** v účtu Batch. Pokud chcete propojit účet úložiště s účtem Batch, v okně **Upozornění** vyberte **účet úložiště** a pak znovu vyberte **účet úložiště** .

Po propojení těchto dvou účtů může služba Batch automaticky nasadit balíčky uložené v propojeném účtu úložiště do vašich výpočetních uzlů.

> [!IMPORTANT]
> Balíčky aplikací se nedají použít s účty Azure Storage nakonfigurovanými s [pravidly brány firewall](../storage/common/storage-network-security.md)nebo s **hierarchickým oborem názvů** nastaveným na **povoleno**.

Služba Batch používá Azure Storage k ukládání balíčků aplikací jako objektů blob bloku. Poplatky za data objektů blob bloku se [účtují jako normální](https://azure.microsoft.com/pricing/details/storage/) a velikost každého balíčku nemůže překročit maximální velikost objektu blob bloku. Další informace najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle pro účty úložiště](../storage/blobs/scalability-targets.md). Chcete-li minimalizovat náklady, je nutné vzít v úvahu velikost a počet balíčků aplikací a pravidelně odebírat zastaralé balíčky.

### <a name="view-current-applications"></a>Zobrazit aktuální aplikace

Pokud chcete zobrazit aplikace ve vašem účtu Batch, v levé navigační nabídce vyberte **aplikace** .

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Snímek obrazovky s položkou nabídky aplikace v Azure Portal":::

Po výběru této možnosti nabídky se otevře okno **aplikace** . V tomto okně se zobrazuje ID jednotlivých aplikací ve vašem účtu a následující vlastnosti:

- **Balíčky**: počet verzí přidružených k této aplikaci.
- **Výchozí verze**: Pokud je to možné, verze aplikace, která se nainstaluje, když při nasazování aplikace nebude zadána žádná verze
- **Povolit aktualizace**: Určuje, jestli jsou povolené aktualizace balíčků a odstranění.

Pokud chcete zobrazit [strukturu souborů](files-and-directories.md) balíčku aplikace na výpočetním uzlu, přejděte na účet Batch v Azure Portal. Vyberte **fondy** a potom vyberte fond, který obsahuje výpočetní uzel, který vás zajímá. Pak vyberte výpočetní uzel, na kterém je nainstalován balíček aplikace, a otevřete složku **aplikace** .

### <a name="view-application-details"></a>Zobrazit podrobnosti o aplikaci

Chcete-li zobrazit podrobnosti o aplikaci, vyberte ji v okně **aplikace** . Pro svou aplikaci můžete nakonfigurovat následující nastavení.

- **Allow Updates**: Určuje, zda lze balíčky aplikací [aktualizovat nebo odstranit](#update-or-delete-an-application-package). Výchozí hodnota je **Yes** (Ano). Pokud je tato možnost nastavená na **ne**, aktualizace balíčku a odstranění se pro aplikaci nepovolují, i když můžete přidat nové verze balíčku aplikace.
- **Výchozí verze**: výchozí balíček aplikace, který se má použít při nasazení aplikace, pokud není zadaná žádná verze.
- **Zobrazovaný název**: popisný název, který může vaše řešení Batch použít, když zobrazuje informace o aplikaci. Tento název lze například použít v uživatelském rozhraní služby, které poskytnete vašim zákazníkům prostřednictvím služby Batch.

### <a name="add-a-new-application"></a>Přidat novou aplikaci

Chcete-li vytvořit novou aplikaci, přidejte balíček aplikace a zadejte nové jedinečné ID aplikace.

V účtu Batch vyberte **aplikace** a pak vyberte **Přidat**.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Snímek obrazovky procesu vytváření nové aplikace v Azure Portal.":::

Zadejte následující informace:

- **ID aplikace**: ID vaší nové aplikace.
- **Version (verze**): verze balíčku aplikace, kterou nahráváte.
- **Balíček aplikace**: soubor. zip obsahující binární soubory aplikace a podpůrné soubory, které jsou nutné ke spuštění aplikace.

ID a **verze** **aplikace** , které zadáte, musí splňovat tyto požadavky:

- V uzlech systému Windows ID může obsahovat libovolnou kombinaci alfanumerických znaků, spojovníků a podtržítka. V uzlech systému Linux jsou povoleny pouze alfanumerické znaky a podtržítka.
- Nemůže obsahovat více než 64 znaků.
- Musí být v rámci účtu Batch jedinečný.
- V ID se nerozlišují velká a malá písmena.

Až budete připraveni, vyberte **Odeslat**. Po nahrání souboru zip do účtu Azure Storage na portálu se zobrazí oznámení. V závislosti na velikosti souboru, který nahráváte, a rychlosti síťového připojení může to trvat delší dobu.

### <a name="add-a-new-application-package"></a>Přidat nový balíček aplikace

Pokud chcete přidat verzi balíčku aplikace pro existující aplikaci, vyberte aplikaci v části **aplikace** účtu Batch a pak vyberte **Přidat**.

Stejně jako u nové aplikace zadejte **verzi** nového balíčku, nahrajte soubor. zip do pole **balíček aplikace** a pak vyberte **Odeslat**.

### <a name="update-or-delete-an-application-package"></a>Aktualizace nebo odstranění balíčku aplikace

Chcete-li aktualizovat nebo odstranit existující balíček aplikace, vyberte aplikaci v části **aplikace** účtu Batch. Vyberte tři tečky na řádku balíčku aplikace, který chcete upravit, a pak vyberte akci, kterou chcete provést.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Snímek obrazovky znázorňující možnosti aktualizace a odstranění pro balíčky aplikací v Azure Portal.":::

Pokud vyberete možnost **aktualizovat**, budete moct nahrát nový soubor. zip. Tím se nahradí předchozí soubor zip, který jste nahráli pro danou verzi.

Pokud vyberete **Odstranit**, zobrazí se výzva k potvrzení odstranění této verze. Jakmile vyberete **OK**, Batch odstraní soubor. zip z účtu Azure Storage. Odstraníte-li výchozí verzi aplikace, bude pro tuto aplikaci odebrána **výchozí nastavení verze** .

## <a name="install-applications-on-compute-nodes"></a>Instalace aplikací na výpočetní uzly

Teď, když jste se naučili, jak spravovat balíčky aplikací v Azure Portal, můžeme diskutovat o tom, jak je nasadit do výpočetních uzlů a jak je spouštět s úkoly Batch.

### <a name="install-pool-application-packages"></a>Instalovat balíčky aplikací fondu

Chcete-li nainstalovat balíček aplikace na všech výpočetních uzlech ve fondu, zadejte jeden nebo více odkazů na balíčky aplikací pro daný fond. Balíčky aplikací, které zadáte pro fond, se nainstalují do každého výpočetního uzlu, když se tento uzel připojí k fondu, a když se uzel restartuje nebo obnoví z image.

Při vytváření nového fondu nebo pro stávající fond zadejte v dávce .NET jednu nebo více [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) . Třída [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) Určuje ID a verzi aplikace, která se má nainstalovat na výpočetní uzly fondu.

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
> Pokud se nasazení balíčku aplikace z nějakého důvodu nepovede, služba Batch označí uzel jako [nepoužitelný](/dotnet/api/microsoft.azure.batch.computenode.state)a žádné úlohy se naplánují pro provádění na tomto uzlu. V takovém případě byste měli restartovat uzel a znovu zahájit nasazení balíčku. Restartování uzlu také umožňuje znovu naplánovat úlohu na uzlu.

### <a name="install-task-application-packages"></a>Instalovat balíčky aplikací úloh

Podobně jako fond určujete odkazy na balíčky aplikací pro úlohu. Když je naplánováno spuštění úlohy na uzlu, balíček je stažen a extrahován těsně před provedením příkazového řádku úkolu. Pokud je v uzlu již nainstalován zadaný balíček a verze, balíček se nestáhne a použije se existující balíček.

Chcete-li nainstalovat balíček aplikace úkolu, nakonfigurujte vlastnost [CloudTask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) úlohy:

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

V uzlech se systémem Linux se formát mírně liší. Tečky (.), spojovníky (-) a znaménko čísla (#) jsou shrnuty do podtržítek v proměnné prostředí. Všimněte si také, že se zachová případ ID aplikace. Příklad:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` a `version` jsou hodnoty, které odpovídají verzi aplikace a balíčku, který jste zadali pro nasazení. Pokud jste například zadali, že by měla být na uzlech systému Windows nainstalována verze 2,7 nástroje *Blend* pro aplikace, budou příkazové řádky úlohy používat pro přístup ke svým souborům tuto proměnnou prostředí:

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
> Další informace o nastavení prostředí výpočetních uzlů najdete v tématu [nastavení prostředí pro úlohy](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu

Pokud je už existující fond nakonfigurovaný pomocí balíčku aplikace, můžete pro tento fond zadat nový balíček. Pokud zadáte nový odkaz na balíček pro fond, platí následující:

- Služba Batch nainstaluje nově zadaný balíček na všech nových uzlech, které se připojí k fondu, a na všech stávajících uzlech, které se restartují nebo obnoví z image.
- Výpočetní uzly, které už jsou ve fondu, když aktualizujete odkazy na balíček, neinstalují automaticky nový balíček aplikace. Aby bylo možné získat nový balíček, musí se tyto výpočetní uzly restartovat nebo obnovit z image.
- Při nasazení nového balíčku odrážejí vytvořené proměnné prostředí odkazy na nové balíčky aplikací.

V tomto příkladu má existující fond verzi 2,7 aplikace *Blendu* nakonfigurovanou jako jednu z jeho [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Chcete-li aktualizovat uzly fondu pomocí verze 2.76 b, zadejte nový [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) s novou verzí a potvrďte změnu.

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

Teď, když je nová verze nakonfigurovaná, služba Batch nainstaluje verzi 2.76 b na jakýkoliv nový uzel, který se připojí k fondu. Pokud chcete nainstalovat 2.76 b na uzlech, které už jsou ve fondu, restartujte je nebo je znovu naimagí. Všimněte si, že restartované uzly uchovávají soubory z předchozích nasazení balíčků.

## <a name="list-the-applications-in-a-batch-account"></a>Výpis aplikací v účtu Batch

Pomocí metody [ApplicationOperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) můžete vypsat aplikace a jejich balíčky v účtu Batch.

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

## <a name="next-steps"></a>Další kroky

- [REST API dávky](/rest/api/batchservice) také poskytují podporu pro práci s balíčky aplikací. Podívejte se například na element [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) , kde můžete určit balíčky k instalaci a [aplikace](/rest/api/batchservice/application) , jak získat informace o aplikaci.
- Naučte se programově [Spravovat účty Azure Batch a kvóty pomocí rozhraní Batch Management .NET](batch-management-dotnet.md). Knihovna [.NET Batch Management](/dotnet/api/overview/azure/batch/management) může povolit funkce vytváření a odstraňování účtů pro vaši aplikaci nebo službu Batch.
