---
title: Instalovat balíčky aplikací na výpočetních uzlech – Azure Batch | Dokumentace Microsoftu
description: Použití funkce balíčky aplikací služby Azure Batch můžete snadno spravovat více aplikací a verzí pro instalaci na Batch výpočetních uzlů.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/15/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fd3eccf3de5d46520dc5a50cab66667c875799e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454603"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch

Funkce balíčky aplikací služby Azure Batch poskytuje snadnou správu aplikací úloh a jejich nasazení do výpočetních uzlů ve fondu. Pomocí balíčků aplikací můžete odesílat a spravovat více verzí aplikací, které vaše úkoly spouštět, včetně jejich podpůrné soubory. Můžete pak automaticky nasadit jednu nebo více těchto aplikací do výpočetních uzlů ve fondu.

V tomto článku se dozvíte, jak můžete odesílat a spravovat balíčky aplikací na webu Azure Portal. Pak se naučíte k instalaci na vytvoření fondu výpočetních uzlů s [Batch .NET] [ api_net] knihovny.

> [!NOTE]
> 
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací.
>
> Rozhraní API pro vytváření a správu balíčky aplikací jsou součástí [rozhraní Batch Management .NET] [ api_net_mgmt] knihovny. Rozhraní API pro instalaci balíčků aplikací na výpočetní uzly jsou součástí [Batch .NET] [ api_net] knihovny. V dostupných rozhraní API služby Batch pro ostatní jazyky jsou srovnatelné funkce. 
>
> Funkce balíčky aplikací, je zde popsáno, nahrazuje dostupnými v předchozích verzích služby Batch aplikace funkcí.
> 
> 

## <a name="application-package-requirements"></a>Požadavky na balíček aplikace
Chcete-li používat balíčky aplikací, je potřeba [propojit účet Azure Storage](#link-a-storage-account) k účtu Batch.

## <a name="about-applications-and-application-packages"></a>Informace o aplikacích a balíčcích aplikací
V rámci služby Azure Batch *aplikace* odkazuje na sadu verze binárních souborů, které se automaticky stáhnou do výpočetních uzlů ve fondu. *Balíčku aplikace* odkazuje na *konkrétní sady* tyto binární soubory a představuje daný *verze* aplikace.

![Vysokoúrovňový diagram aplikacích a balíčcích aplikací][1]

### <a name="applications"></a>Aplikace
Aplikace ve službě Batch obsahuje jeden nebo více aplikací, balíčky a určuje možnosti konfigurace pro aplikaci. Aplikace můžete například určit výchozí verze balíčku aplikace k instalaci na výpočetních uzlech a určuje, zda jeho balíčky můžete aktualizovat ani odstranit.

### <a name="application-packages"></a>Balíčky aplikací
Balíček aplikace je soubor .zip, který obsahuje binární soubory aplikace a podpůrné soubory, které jsou požadovány pro své úkoly ke spuštění aplikace. Každý balíček aplikace představuje konkrétní verzi aplikace.

Určíte balíčky aplikací na úrovni fondu nebo úkolu. Při vytváření fondu nebo úkolu můžete zadat jednu nebo více z těchto balíčků a (volitelně) verze.

* **Balíčky aplikací fondu** jsou nasazené na *každý* uzlu ve fondu. Aplikace se nasadí, když se uzel připojí fondu a když ho restartuje nebo obnoví z Image.
  
    Balíčky aplikací fondu jsou odpovídající po provedení všech uzlů ve fondu úkolů úlohy. Při vytváření fondu, a můžete přidat nebo aktualizovat balíčky existujícího fondu, můžete určit jeden nebo více balíčků aplikací. Při aktualizaci existujícího fondu, balíčky aplikací, je nutné restartovat jeho uzly k instalaci nového balíčku.
* **Balíčky aplikací úkolů** jsou nasazeny pouze na výpočetním uzlu naplánované spuštění úlohy, stačí před spuštěním příkazového řádku úkolu. Pokud zadaný balíček aplikace a verze je již na uzlu, se znovu nasadit a používá se existující balíček.
  
    Balíčky aplikací úkolů jsou užitečné v prostředích sdíleného fondu, různé úlohy spouštějí v jednom fondu, kde se fond neodstraní po dokončení úlohy. Pokud má vaše úloha méně úkolů, než je uzlů ve fondu, balíčky aplikací úkolů můžou omezit přenosy dat, protože se aplikace může nasadit jen na uzly, které úkoly budou skutečně provádět.
  
    Další scénáře, které může přinést balíčky aplikací úkolů jsou úlohy, které spouštějí rozsáhlé aplikace, ale pouze několik úloh. Například fázi předběžného zpracování nebo úlohu sloučení, ve kterém je aplikace předběžného zpracování nebo sloučení těžký, mohou s výhodou využít balíčky aplikací úkolů.

> [!IMPORTANT]
> Existují omezení počtu aplikacích a balíčcích aplikací v rámci účtu Batch a aplikace maximální velikost balíčku. Zobrazit [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) podrobnosti o těchto omezeních.
> 
> 

### <a name="benefits-of-application-packages"></a>Výhody balíčky aplikací
Balíčky aplikací můžete zjednodušit kód, který do vašeho řešení Batch a snížení režie nezbytné ke správě aplikací, které vaše úkoly spouštět.

Pomocí balíčků aplikací nemusí váš fond spouštěcí úkol určit dlouhý seznam jednotlivých prostředků soubory pro instalaci na uzlech. Není nutné ručně spravovat více verzí souborů aplikace ve službě Azure Storage, nebo na svých uzlech. A není nutné se starat o generování [adres URL SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) a zajistit tak přístup k souborům ve vašem účtu úložiště. Služba batch pracuje s Azure Storage k ukládání balíčků aplikací a nasazovat je do výpočetních uzlů na pozadí.

> [!NOTE] 
> Celková velikost spouštěcího úkolu nesmí přesahovat 32768 znaků, včetně souborů prostředků a proměnných prostředí. Pokud spouštěcí úkol překročí tento limit, pak pomocí balíčků aplikací je další možností. Můžete také vytvořit komprimovaný archiv obsahující vaše soubory prostředků, nahrajte ho jako objekt blob do služby Azure Storage a rozbalte ho z příkazového řádku spouštěcí úkol. 
>
>

## <a name="upload-and-manage-applications"></a>Odesílat a spravovat aplikace
Můžete použít [webu Azure portal] [ portal] nebo rozhraní API služby Batch Management ke správě balíčků aplikací v účtu Batch. V následujících částech budeme nejprve ukazují, jak propojit účet úložiště a diskutovat o přidání aplikací a balíčků a správa pomocí portálu.

### <a name="link-a-storage-account"></a>Propojí účet služby Storage
Použití balíčků aplikací, musíte si nejdříve propojit [účtu služby Azure Storage](batch-api-basics.md#azure-storage-account) k účtu Batch. Pokud jste ještě nenakonfigurovali účet úložiště, na webu Azure portal zobrazí upozornění při prvním kliknete **aplikací** ve svém účtu Batch.



![Upozornění 'Nakonfigurovaný žádný účet úložiště' na webu Azure portal][9]

Služba Batch používá přidružený účet úložiště k ukládání balíčky aplikací. Jakmile propojíte dva účty, služby Batch můžete automaticky nasadit balíčky uložených v propojeném účtu úložiště do výpočetních uzlů. Chcete-li s účtem Batch propojit účet úložiště, klikněte na tlačítko **účtu úložiště** na **upozornění** okna a pak klikněte na tlačítko **účtu úložiště** znovu.

![Na webu Azure portal vyberte okno účet úložiště][10]

Doporučujeme vytvořit účet úložiště *konkrétně* pro použití s vaším účtem Batch a vyberte ho tady. Po vytvoření účtu úložiště, si můžete propojit ho k účtu Batch pomocí **účtu úložiště** okna.

> [!NOTE] 
> Momentálně nelze použít balíčky aplikací pomocí účtu Azure Storage, který je nakonfigurovaný s [pravidla brány firewall](../storage/common/storage-network-security.md).
> 

Služba Batch používá Azure Storage k ukládání balíčky aplikací jako objekty BLOB bloku. Jste [účtovat jako za normálních okolností] [ storage_pricing] pro objekt blob bloku dat a velikost každého balíčku nesmí přesáhnout [velikost objektu blob bloku maximální](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets). Je potřeba vzít v úvahu velikost a počet balíčky aplikací a pravidelně odebrat zastaralé balíčky, které umožní minimalizovat náklady.
> 
> 

### <a name="view-current-applications"></a>Zobrazit aktuální aplikace
Chcete-li zobrazit aplikace v účtu Batch, klikněte na tlačítko **aplikací** položky nabídky v nabídce vlevo při zobrazení vaší **účet Batch**.

![Dlaždice aplikace][2]

Výběrem této možnosti se otevře **aplikací** okno:

![Výpis aplikací][3]

Toto okno zobrazuje ID každé aplikace v účtu a následující vlastnosti:

* **Balíčky**: Číslo verze přidružené k této aplikaci.
* **Výchozí verze**: Verze aplikace nainstalovat, pokud při zadávání žádosti o fond nevyžadují verze. Toto nastavení je volitelné.
* **Povolit aktualizace**: Hodnota, která určuje, jestli balíček aktualizace, odstranění a přidání jsou povoleny. Pokud je nastavené na **ne**, balíček aktualizace a odstranění jsou zakázané pro aplikaci. Je možné přidat pouze nové verze balíčku aplikace. Výchozí hodnota je **Yes** (Ano).

### <a name="view-application-details"></a>Zobrazení podrobností aplikace
Chcete-li zobrazit podrobnosti o aplikaci, vyberte aplikaci v **aplikací** okna.

![Podrobnosti o aplikaci][4]

V podrobnostech aplikací můžete nakonfigurovat následující nastavení pro vaši aplikaci.

* **Povolit aktualizace**: Určete, zda jeho balíčky aplikací můžete aktualizovat ani odstranit. Dále v tomto článku najdete v článku "Aktualizace nebo odstranění balíčku aplikace".
* **Výchozí verze**: Zadejte výchozí balíček aplikace pro nasazení do výpočetních uzlů.
* **Zobrazovaný název**: Zadejte popisný název, který vaše řešení Batch můžete použít při zobrazení informací o aplikaci, například v uživatelském rozhraní služby poskytovat vašim zákazníkům prostřednictvím služby Batch.

### <a name="add-a-new-application"></a>Přidejte novou aplikaci
Chcete-li vytvořit novou aplikaci, přidání balíčku aplikace a zadat ID nové, jedinečné aplikace. První balíčku aplikace, které můžete přidat pomocí nového ID aplikace také vytvoří novou aplikaci.

Klikněte na **Aplikace** > **Přidat**.

![Nové okno aplikace na webu Azure portal][5]

**Novou aplikaci** okno obsahuje následující pole a zadejte nastavení nové aplikace a balíček aplikace.

**Id aplikace**

Toto pole určuje ID vaší nové aplikace, který je v souladu s standardní ověřovací pravidla ID dávky Azure. Pravidla pro zajištění ID aplikace jsou následující:

* Na uzlech Windows toto ID může obsahovat libovolnou kombinaci alfanumerické znaky, spojovníky a podtržítka. Na uzly s Linuxem jsou povolené jenom alfanumerické znaky nebo podtržítka.
* Nesmí obsahovat víc než 64 znaků.
* Musí být jedinečný v rámci účtu Batch.
* Je zachování případ a velká a malá písmena.

**Verze**

Toto pole určuje verzi balíčku aplikace, které nahráváte. Řetězce verze se vztahují následující pravidla ověřování:

* Na uzlech Windows verze řetězec může obsahovat libovolnou kombinaci alfanumerických znaků, pomlčky, podtržítka a tečky. Na uzly s Linuxem řetězce verze může obsahovat jenom alfanumerické znaky nebo podtržítka.
* Nesmí obsahovat víc než 64 znaků.
* Musí být jedinečný v rámci aplikace.
* Jsou zachována a velká a malá písmena.

**Balíček aplikace**

Toto pole určuje soubor .zip, který obsahuje binární soubory aplikace a podpůrné soubory, které jsou nutné ke spuštění aplikace. Klikněte na tlačítko **vyberte soubor** pole nebo na ikonu složky a vyhledejte a vyberte soubor .zip, který obsahuje soubory vaší aplikace.

Po výběru souboru, klikněte na tlačítko **OK** a zahajte nahrávání do služby Azure Storage. Po dokončení operace nahrávání na portálu se zobrazí oznámení. V závislosti na rychlosti připojení k síti a velikost souboru, který odesíláte tato operace může trvat nějakou dobu.

> [!WARNING]
> Nezavírejte **novou aplikaci** okno před dokončením operace nahrávání. Tím se zastaví proces nahrávání.
> 
> 

### <a name="add-a-new-application-package"></a>Přidat nový balíček aplikace
Pokud chcete přidat verze balíčku aplikace pro existující aplikace, vyberte aplikaci v **aplikací** windows a klikněte na **balíčky** > **přidat**.

![Přidat okno balíčku aplikací na webu Azure portal][8]

Jak je vidět, pole odpovídají objektu **novou aplikaci** okna, ale **id aplikace** pole je zakázaná. Stejně jako nové aplikace, zadejte **verze** pro nový balíček, přejděte do vaší **balíčku aplikace** .zip soubor a potom klikněte na **OK** pro nahrání balíčku.

### <a name="update-or-delete-an-application-package"></a>Aktualizace nebo odstranění balíčku aplikace
Aktualizovat nebo odstranit existující balíček aplikace, otevřete podrobnosti o aplikaci, klikněte na tlačítko **balíčky**, klikněte na tlačítko **tlačítko se třemi tečkami** v řádku, který chcete upravit a vyberte balíček aplikace Akce, kterou chcete provést.

![Aktualizace nebo odstranění balíčku na webu Azure portal][7]

**Aktualizace**

Po kliknutí na **aktualizace**, **balíček aktualizace** se zobrazí okno. Toto okno se podobá **nový balíček aplikace** okna, ale pouze pole výběru balíčku je povoleno, vám umožní zadat nový soubor ZIP k nahrání.

![Aktualizovat balíček okna portálu Azure portal][11]

**Odstranění**

Po kliknutí na **odstranit**, zobrazí se výzva k potvrzení odstranění balíčku verze a Batch odstraní balíček ze služby Azure Storage. Pokud odstraníte výchozí verzi aplikace, **výchozí verze** odebrat nastavení pro aplikaci.

![Odstranit aplikaci ][12]

## <a name="install-applications-on-compute-nodes"></a>Instalace aplikací na výpočetní uzly
Teď, když jsme zjistili, jak spravovat balíčky aplikací pomocí webu Azure portal, můžete si popíšeme, jak nasadit do výpočetních uzlů a spusťte je s úkoly služby Batch.

### <a name="install-pool-application-packages"></a>Instalovat balíčky aplikací fondu
Chcete-li nainstalovat balíček aplikace na všech výpočetních uzlech ve fondu, zadejte balíček aplikace jeden nebo více *odkazy* pro fond. Balíčky aplikací, které zadáte pro fond jsou nainstalovány na každém výpočetním uzlu, když se takový uzel připojí k fondu a když se uzel restartuje nebo obnoví z Image.

V rozhraní .NET služby Batch, zadejte jeden nebo více [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] při vytvoření nového fondu, nebo pro už existující fond. [ApplicationPackageReference] [ net_pkgref] třída určuje ID aplikace a verze pro instalaci na vytvoření fondu výpočetních uzlů.

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
> Pokud z nějakého důvodu selže nasazení balíčku aplikace, služba Batch označí uzel [nepoužitelné][net_nodestate], a nejsou žádné úkoly jsou naplánovány k provedení v tomto uzlu. V takovém případě byste měli **restartovat** uzel, který má inicializujte nasazení balíčku. Restartování uzlu taky umožňuje znovu plánování úkolů na uzlu.
> 
> 

### <a name="install-task-application-packages"></a>Instalovat balíčky aplikací úkolů
Podobně jako u fondu, zadejte balíček aplikace *odkazy* pro úlohu. Když je úloha naplánována ke spuštění na uzlu, balíček stáhnout a extrahovat těsně před provedením příkazového řádku úkolu. Pokud zadaného balíčku a verzi je již nainstalován na uzlu, balíček se nestáhne, a používá se existující balíček.

Instalace balíčku aplikace úkolu, nakonfigurujte úkolu [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] vlastnost:

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
Balíčky, které jste zadali pro fond nebo úloh jsou stažené a rozbalené na adresář s názvem v rámci `AZ_BATCH_ROOT_DIR` uzlu. Služba Batch také vytvoří proměnné prostředí, který obsahuje cestu k adresáři s názvem. Příkazové řádky úkolu použijte tuto proměnnou prostředí při odkazování na aplikace na uzlu. 

Na uzlech Windows je proměnná v následujícím formátu:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Formát na uzly s Linuxem, je mírně liší. Tečky (.), spojovníky (-) a symboly čísla (#) se sloučí do podtržítka v proměnné prostředí. Všimněte si také, že případ ID aplikace je zachováno. Příklad:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` a `version` jsou hodnoty, které odpovídají verzi aplikací a balíčků, jste zadali pro nasazení. Například, pokud jste zadali této verze 2.7 aplikace *blenderu* by měly být nainstalovány na uzlech Windows příkazové řádky úkolu by použití této proměnné prostředí pro přístup k jeho soubory:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

V uzlech systému Linux zadejte proměnnou prostředí v tomto formátu. Sloučit tečky (.), spojovníky (-) a znaky čísla (#) k podtržítka a zachovat tak ID aplikace:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Při nahrávání balíčku aplikace, můžete určit výchozí verzi pro nasazení do výpočetních uzlů. Pokud jste určili výchozí verze pro aplikaci, můžete vynechat přípony verze při odkazu aplikaci. Můžete určit výchozí verzi aplikace na webu Azure Portal, v **aplikací** okna, jak je znázorněno v [odesílat a spravovat aplikace](#upload-and-manage-applications).

Například pokud nastavíte jako výchozí verze pro aplikaci "2.7" *blenderu*a následující proměnné prostředí odkazovat na vaše úkoly a pak uzly Windows se spustí verze 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Následující fragment kódu ukazuje příklad příkazového řádku úkolu, který se spustí výchozí verzi *blenderu* aplikace:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Zobrazit [nastavení prostředí pro úkoly](batch-api-basics.md#environment-settings-for-tasks) v [přehled funkcí Batch](batch-api-basics.md) Další informace o nastavení prostředí výpočetního uzlu.
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu
Pokud existujícího fondu je již nakonfigurován s balíčkem aplikace, můžete zadat nový balíček pro fond. Pokud zadáte nový odkaz na balíček pro fond, následujících podmínek:

* Služba Batch nainstaluje nově zadaný balíček na všechny nové uzly, které se připojí k fondu a na jakékoli existující uzel, který restartuje nebo obnoví z Image.
* Výpočetní uzly, které jsou již ve fondu, když aktualizujete odkazy na balíček automaticky neinstaluje nového balíčku aplikace. Tyto výpočetní uzly musí restartuje nebo obnoví z Image pro příjem nový balíček.
* Po nasazení nového balíčku vytvořené proměnné, aby odrážela nové odkazy na balíček aplikace.

V tomto příkladu má existující fond verze 2.7 *blenderu* aplikace konfigurovaná jako jeden z jeho [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. S verzí 2.76b aktualizovat uzly fondu, zadejte nový [ApplicationPackageReference] [ net_pkgref] s novou verzí a potvrzení změn.

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

Teď, když byl nakonfigurován na novou verzi, služba Batch nainstaluje kterékoli verzi 2.76b *nové* uzel, který se připojí k fondu. Chcete-li nainstalovat 2.76b na uzly, které jsou *již* ve fondu, restartovat nebo obnovit z Image je. Všimněte si, že restartovaná uzly zachovat soubory z předchozích nasazení balíčku.

## <a name="list-the-applications-in-a-batch-account"></a>Seznam aplikací v účtu Batch
Můžete vytvořit seznam aplikací a balíčků v účtu Batch pomocí [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metody.

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
S balíčky aplikací, které pomáhají zákazníkům vybrat aplikace pro svou práci a určit přesné verze se má použít při zpracování úloh pomocí služby podporují službu Batch. Můžete také zadat možnost pro vaše zákazníky k nahrání a sledovat jejich vlastních aplikací ve službě.

## <a name="next-steps"></a>Další postup
* [Rozhraní REST API služby Batch] [ api_rest] také poskytuje podporu pro práci s balíčky aplikací. Viz například [applicationPackageReferences] [ rest_add_pool_with_packages] prvek [přidání fondu k účtu] [ rest_add_pool] informace o tom, jak zadat balíčky pro instalaci s použitím rozhraní REST API. Zobrazit [aplikací] [ rest_applications] podrobné informace o tom, jak získat informace o aplikaci pomocí rozhraní REST API služby Batch.
* Zjistěte, jak prostřednictvím kódu programu [Správa účtů služby Azure Batch a kvót pomocí rozhraní Batch Management .NET](batch-management-dotnet.md). [Rozhraní Batch Management .NET] [ api_net_mgmt] knihovny můžete povolit funkce vytváření a odstraňování účtu služby Batch aplikace nebo služby.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Vysokoúrovňový diagram balíčky aplikací"
[2]: ./media/batch-application-packages/app_pkg_02.png "Dlaždice aplikace na webu Azure portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Okno aplikace na webu Azure portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Podrobnosti o okně aplikace na webu Azure portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nové okno aplikace na webu Azure portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Aktualizace nebo odstranění balíčků rozevírací seznam na webu Azure portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nové okno balíčku aplikace na webu Azure portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Žádná výstraha propojeného účtu úložiště"
[10]: ./media/batch-application-packages/app_pkg_10.png "Na webu Azure portal vyberte okno účet úložiště"
[11]: ./media/batch-application-packages/app_pkg_11.png "Aktualizovat balíček okna portálu Azure portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Odstranit balíček potvrzovací dialogové okno na webu Azure portal"
